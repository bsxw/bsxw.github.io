---
layout: post
title: ABB softmove选项
categories: ABB
description: 机器人软件选项
keywords: ABB 机器人 softmove 软伺服
---
**必备条件:机器人开通 885-1 soft move功能选项**

**同时以下功能失效**

- 碰撞检测（613-1）
- 力控（661-2）
- 区域输出（608-1）
- 输送带跟踪（606-1）

**stifness: 软方向刚性（多大百分比力返回原位置：机器人静止时位置或运动时的目标位置）。**

**stiffnessnoSoftDir:非软方向刚性。**

**非软方向刚性必须大于软方向刚性。**

**实际刚性与实际阻尼值计算格式**
- 例如软方向实际刚性：=stiffness_min + (stiffness/100)\*(stiffness_max - stiffness_min);
- 按默认设置即：0+(stiffness/100)\*(10-0);
- 例如软方向实际阻尼：=stiffness/(Stiffness to damping ratio);
- 按默认设置即：stiffness/0.5;

**CSSAct指令不带\AllowMove**

- stiffness_min:软方向最小刚性(范围：0-100；默认值：**0**)
- stiffness_non_soft_dir_min:非软方向最小刚性(范围：0-100；默认值：**5**)
- stiffness_max:软方向最大刚性(范围：0-100；默认值：**10**)
- stiffness_non_soft_dir_max:非软方向最大刚性(范围：0-100；默认值：**20**)

**CSSAct指令带\AllowMove**

- stiffness_min_move:软方向最小刚性(范围：0-100；默认值：**2**)
- stiffness_non_soft_dir_min_move:非软方向最小刚性(范围：0-100；默认值：**10**)
- stiffness_max_move:软方向最大刚性(范围：0-100；默认值：**10**)
- stiffness_non_soft_dir_max_move:非软方向最大刚性(范围：0-100；默认值：**20**)

**Reframe:参考笛卡尔坐标(仅工件坐标CSS_REFRAME_WOBJ或工具坐标CSS_REFRAME-TOOL)。**

**css_soft_dir共8个：CSS_X,CXX_Y,CSS_Z,CSS_XY,CSS_XZ,CSS_YZ,CSS-XYZ,CSS_XYRZ;**

**css_offset_dir共6个：CSS_POSX,CSS_POSY,CSS_POSZ,CSS_NEGX,CSS_NEGY,CSS_NEGZ;**

**相应指令**

- CSSAct:激活相应笛卡尔坐标对应方向软伺服功能。
- CSSDeactMoveL：关闭软伺服功能。
- CSSForceOffTune:计算摩擦补偿力大小。
- CSSForceOffSetAct:激活力补偿。
- CSSForceOffSetDeact:关闭力补偿（CSSDeactMoveL指令执行后同样关闭摩擦补偿）。

**软伺服、摩擦补偿注意事项**

- stop:软伺服仍保持，摩擦补偿失效。
- Montor Off:软伺服失效再次Motor ON会恢复。

**案例程序**

**主模块中声明**

```
    PERS num lengthPlug:=3.80607;
    PERS robtarget laydown_dest;
    PERS robtarget liftout_dest;
    PERS loaddata load_small := [12, [0, 0, 90],[1, 0, 0, 0], 0, 0, 0];
    PERS loaddata load_big := [36, [0, 0, 210],[1, 0, 0, 0], 0, 0, 0];
```

**程序中调用**

```
PROC buffer_soft()                                                                      //两个暂存台的软取放（每个暂存台含一大一小两个取放位置）
        VAR num temp_position;
        VAR num laydown_num:=0;
        VAR num liftout_num:=0;
        temp_position:=1;
        ......
        buffer_laydown:                                                                 //软放下
        laydown_dest:=lay_Soft(P_Temp_pos_soft{temp_position},20\stiff:=0\shift:=100);  //参考工具坐标，软方向刚性0，非软方向刚性100
        IF NOT Check(laydown_dest,P_Temp_pos_soft{temp_position}\length:=0.6) THEN          
            Incr laydown_num;
            IF laydown_num<5 THEN                                                      //尝试5次不成功则停止
                GOTO buffer_laydown; 
            ELSE
                TPWrite "soft move distance:"\Num:=lengthPlug;                         //显示机器人当前位置（软运动后停止）与目标点的距离
                TPWrite("laydown failed!");
            stop;
            ENDIF
        ELSEIF Check(laydown_dest,P_Temp_pos_soft{temp_position}\length:=0.6) THEN
            TPWrite "soft move distance:"\Num:=lengthPlug;
            TPWrite("laydown suceessed!");
        ENDIF
        WaitTime\InPos,0;
        SetDO c_gripper_close,0;
        SetDO c_gripper_open,1;
        WaitDI gripper_product_sensor,0;
        GripLoad load0;
        ......
        buffer_liftout:                                                                 //软提起
        liftout_dest:=lift_Soft(P_Temp_pos_soft{temp_position},15\stiff:=0\shift:=100); //参考工具坐标，软方向刚性0，非软方向刚性100
        IF NOT Check(liftout_dest,RelTool(P_Temp_pos_soft{temp_position},0,15,0)\length:=2) THEN
            Incr liftout_num;
            IF liftout_num<5 THEN
                GOTO buffer_liftout; 
            ELSE
                TPWrite "soft move distance:"\Num:=lengthPlug;
                TPWrite("lift failed!");
            stop;
            ENDIF
        ELSEIF Check(liftout_dest,RelTool(P_Temp_pos_soft{temp_position},0,15,0)\length:=2) THEN
            TPWrite "soft move distance:"\Num:=lengthPlug;
            TPWrite("lift suceessed!");
        ENDIF
        TEST temp_position
        CASE 2,4:
            GripLoad load_big;                                                        //加载大工件负载
        CASE 1,3:
            GripLoad load_small;                                                      //加载小工件负载
        DEFAULT:
            Stop;
        ENDTEST
       ......
    ENDPROC
```
**1.工具Y方向（垂直向上）软放下**
```
FUNC robtarget lay_Soft(robtarget dest,num dist \num stiff \num shift)                //软放下函数
        VAR robtarget current;
        VAR num stiff_def:=50;
        VAR num shift_def:=0;
        IF Present(stiff) stiff_def:=stiff;
        IF Present(shift) shift_def:=shift;
        //参考CSS_REFFRAME_TOOL(工具坐标)，软方向为CSS_XZ(XZ方向),软方向刚性值stiff_def，非软方向刚性值shift_def,AllowMove(运行运动)。
        CSSAct\RefFrame:=CSS_REFFRAME_TOOL,CSS_XZ\StiffnessNonSoftDir:=shift_def\Stiffness:=stiff_def\AllowMove; 
        MoveL RelTool(dest,0,-2,0),v10,fine,Tool_user\WObj:=Workobject_user;
        !MoveL dest,v50,fine,Tool_user\WObj:=Workobject_user;
        WaitTime 0.5;
        WaitRob \ZeroSpeed;
        current:=CRobT(\Tool:=Tool_user\WObj:=Workobject_user);
        CSSDeactMoveL RelTool(current,0,0,0),v10,Tool_user\WObj:=Workobject_user;
        RETURN current;
    ENDFUNC
```
**2.工具Y方向（垂直向上）软提起**
```
    FUNC robtarget lift_Soft(robtarget dest,num dist \num stiff \num shift)          //软提起函数
        VAR robtarget current;
        VAR num stiff_def:=50;
        VAR num shift_def:=0;
        IF Present(stiff) stiff_def:=stiff;
        IF Present(shift) shift_def:=shift;
        CSSAct\RefFrame:=CSS_REFFRAME_TOOL,CSS_XZ\StiffnessNonSoftDir:=shift_def\Stiffness:=stiff_def\AllowMove;
        MoveL RelTool(dest,0,dist,0),v10,fine,Tool_user\WObj:=Workobject_user;
        WaitTime 0.5;
        WaitRob \ZeroSpeed;
        current:=CRobT(\Tool:=Tool_user\WObj:=Workobject_user);
        CSSDeactMoveL RelTool(current,0,0,0),v10,Tool_user\WObj:=Workobject_user;
        RETURN current;
    ENDFUNC
```
**1.检查是否放、提到位**
```
    FUNC bool Check(robtarget start,robtarget dest \num length)                 //软动作后位置检查函数
        VAR bool result:=FALSE;
        VAR num length_def:=1;
        IF Present(length) length_def:=length;
        lengthPlug:=Distance(start.trans,dest.trans);
        IF (lengthPlug<=length_def) result:=TRUE;
        RETURN result;
    ENDFUNC  
```
