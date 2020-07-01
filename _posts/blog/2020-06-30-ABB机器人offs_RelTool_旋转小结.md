---
layout: post
title: ABB机器人offs_RelTool
categories: ABB
description: 啦啦啦
keywords: ABB，offs，RelTool
---

# ABB机器人offs_RelTool
  
- 偏移函数offs运动参考方向的是所选工件坐标系方向(不可旋转)。
```
  Offs (Point XOffset YOffset ZOffset)
  Point
  数据类型： robtarget
  有待移动的位置数据。
  XOffset
  数据类型：num
  工件坐标系中x方向的位移。
  YOffset
  数据类型：num
  工件坐标系中y方向的位移。
  ZOffset
  数据类型：num
  工件坐标系中z方向的位移。
```
- RelTool（Relative Tool）用于将通过有效工具坐标系表达的位移和/或旋转增加至机械臂位置。
  RelTool (Point Dx Dy Dz [\Rx] [\Ry] [\Rz])
```
  注意
    如果同时指定两个或三个旋转，则旋转将以如下顺序执行：
    围绕x轴旋转
    围绕新y轴旋转
    围绕新z轴旋转
  Point
  数据类型： robtarget
  输入机械臂位置。该位置的方位规定了工具坐标系的当前方位。
  Dx
  数据类型：num
  工具坐标系x方向的位移，以mm计。
  Dy
  数据类型：num
  工具坐标系y方向的位移，以mm计。
  Dz
  数据类型：num
  工具坐标系z方向的位移，以mm计。
  [\Rx]
  数据类型：num
  围绕工具坐标系x轴的旋转，以度计。
  [\Ry]
  数据类型：num
  围绕工具坐标系y轴的旋转，以度计。
  [\Rz]
  数据类型：num
  围绕工具坐标系z轴的旋转，以度计。
  MoveL RelTool (p1, 10, 0, 0 \Rz:= 25), v100, fine, tool1;
    沿工具的x方向，将机械臂移动至距p1达100 mm的一处位置。
    将工具围绕其z轴旋转25°。
```
- 手动重定位操作与工件坐标系无关，参考方向为工具坐标系方向。
