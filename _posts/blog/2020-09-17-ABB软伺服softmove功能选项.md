---
layout: post
title: ABB softmove选项
categories: ABB
description: 机器人软件选项
keywords: ABB 机器人 softmove 软伺服
---
**必备条件:机器人开通 885-1 soft move功能选项**
**同时以下功能失效**

_ 碰撞检测（613-1）
_ 力控（661-2）
_ 区域输出（608-1）
_ 输送带跟踪（606-1）

stifness: 软方向弹力（多大百分比力返回原位置：机器人静止时位置或运动时的目标位置）。
stiffnessnoSoftDir:非软方向弹力。

**CSSAct指令不带\AllowMove**

- stiffness_min:软方向最小弹力
- stiffness_non_soft_dir_min:非软方向最小弹力
- stiffness_max:软方向最大弹力
- stiffness_non_soft_dir_max:非软方向最大弹力

**非软方向弹力必须大于软方向弹力**

**CSSAct指令带\AllowMove**

- stiffness_min_move:软方向最小弹力
- stiffness_non_soft_dir_min_move:非软方向最小弹力
- stiffness_max_move:软方向最大弹力
- stiffness_non_soft_dir_max_move:非软方向最大弹力

**REeframe:参考笛卡尔坐标(仅工件坐标CSS_REFRAME_WOBJ或工具坐标CSS_REFRAME-TOOL)。**

**css_soft_dir共8个：CSS_X,CXX_Y,CSS_Z,CSS_XY,CSS_XZ,CSS_YZ,CSS-XYZ,CSS_XYRZ;**

**css_offset_dir共6个：CSS_POSX,CSS_POSY,CSS_POSZ,CSS_NEGX,CSS_NEGY,CSS_NEGZ;**

**相应指令**

- CSSAct:激活相应笛卡尔坐标对应方向软伺服功能。
- CSSDeactMoveL：关闭软伺服功能。
- CSSForceOffTune:计算摩擦补偿力大小。
- CSSForceOffSetAct:激活力补偿。
- CSSForceOffSetDeact:关闭力补偿。

