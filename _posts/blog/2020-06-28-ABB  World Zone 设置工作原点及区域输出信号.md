---
layout: post
title: ABB机器人World Zone配置
categories: ABB
description: 啦啦啦
keywords: ABB，World Zone，安全区域
---
# ABB World Zone 安全区域设置

- 前提：已安装 608-1 World Zones选项。
- 相关数据类型
   
  - wztemporary
     
  - wztemporary的作用是识别临时全局区域，并可用在RAPID程序中的任何位置。
  - 可通过RAPID指令来禁用、重新启用或擦除临时全局区域。当载入一段新程序时，或当从MAIN例程的起点处开始执行程序时，系统便会自动擦除临时全局区域。
     
  - wzstationary
     
  - wzstationary的作用是识别固定全局区域，并仅能用在与事件“通电”相关联的一则事件例程中。定义事件例程方面的信息请参见操作员手册 - 带 FlexPendant 的 IRC5。
  - 固定全局区域会始终处于激活状态，而重启(先关闭电源然后再打开电源，或更改系统参数)则会再次激活此类区域。无法通过RAPID指令来禁用、启用或擦除固定全局区域。
  - 如果涉及到安全问题，则应使用固定全局区域。
  - shapedata
     
  - shapedata的作用是描述一个全局区域的几何形状。可将全局区域定义为4种不同的几何形状:
  - 一个方盒，所有侧面都与全局坐标系平行
  - 一个圆柱体，与全局坐标系的z轴平行
  - 一个球体
  - 针对机器人轴和/或外轴的一个关节角区
- WZBoxDef - 定义一个箱形全局区域
   
  - WZBoxDef [\Inside] | [\Outside] Shape LowPoint HighPoint
  - WZBoxDef(World Zone Box Definition)用于定义拥有直线箱形状，且各侧均与世界坐标系各轴平行的全局区域。
  - 定义坐标与**世界坐标系**各轴平行，且由对角pos1和pos2所定义的直线箱（ LowPoint、HighPoint为pos数据类型，可使用robtarget的trans变元）。
  - 注意示教LowPoint、HighPoint点是工件坐标与工具的选择。
  - 创建输出信号，注意，该信号的Access Level必须设置为ReadOnly(只读)，设置power on事件event routine关联程序 .
 ```
 PROC home_zone_define()   
    VAR shapedata home_space;
    VAR shapedata allow_move_space;
    CONST jointtarget delta_pos := [ [ 2, 2, 2, 2, 2, 2], [ 5, 9E9, 9E9, 9E9, 9E9, 9E9] ];
    WZHomeJointDef \Inside, home_space, CalcJointT(P_home,Gripper\WObj:=wobj0), delta_pos;
    WZBoxDef \Inside,allow_move_space,P_zone_leftdowm.trans,P_zone_rightup.trans;
    WZDOSet \Stat, home \Inside, home_space, s_home, 1;
    WZDOSet \Stat, zone_allow_move \Inside, allow_move_space, s_zone_allow_move, 1;
ENDPROC
 ```
