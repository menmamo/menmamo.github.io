---
title: "SpriteShape"
author: "mona"
date: 2024-06-20T20:43:00+08:00
categories: ["Unity"]
tags: ["2D地图"]
---

## SpriteShape是用来做什么的？

- SpriteShape是精灵形状的意思
- 它主要是方便我们以节约美术资源为前提制作2D游戏场景地形或者背景的

##  导入SpriteShape工具

1. 在Package Manager中导入相关工具
2. 可以选择性导入事例和拓展资源

## 准备 精灵形状概括资源

- 想要节约美术资源的创建地形或其它类似资源
- 首先我们要准备精灵形状概括资源
- 之后我们就会使用它来创建地形等资源

## SpriteShape的类型

1. 开放不封闭的图形
2. 封闭的图形

## Sprite Shape Profile 精灵形状概述文件配置

- Use Sprite Borders 是否使用精灵边框 用于九宫格拉伸
- Texture 用于填充实心部分的纹理（使用的纹理的平铺模式必须是Repeat重复模式）
- Offset 纹理偏移量
- 这里的设置主要用于封闭的图形 再不同的角度范围使用的图片不同 达到一个封闭效果
  - Angle Ranges 角度范围
  - Start 起始角度
  - End 结束角度
  - Order：Sprite相交时的优先级 优先级高的显示在前面
- Sprites 指定角度范围内的精灵列表 再该角度范围内 可以选择使用的图片资源
- Corners 指定角显示的精灵图片 主要用于封闭图形
  - 外部四个角使用的图片
  - 内部四个角使用的图片

##  Sprite Shape Renderer精灵形状渲染器组件

该控件主要是控制 材质 颜色 以及和其它Sprite交互时的排序等等信息

### 参数

- Color：颜色
- Mask Interaction：遮罩相互作用规则设置
- Fill Material和Edge Material：填充材质和边缘材质
- Sorting Layer和Order in Layer：排序相关

## Sprite Shape Controller精灵形状控制器组件

### 参数

- Profile：使用的精灵形状概述文件
- Detail：精灵形状的质量高中低三种质量
- Is Open Ended：是否是开放的，不封闭的
- Adaptive UV：自适应UV，如果开启，会自动帮助我们判断是平铺还是拉伸
  开启后只有宽度够才会平铺，如果宽度不够会拉伸
  不开启始终平铺，但是可能会出现裁剪效果
  一般根据你的实际效果进行选择
- Optimize Geometry：优化三角形数量，勾选后会最小化精灵图形中的三角形数量
- Enable Tangents：是否启用切线计算功能
  如果你的着色器中需要切线信息参与计算，则勾选
- Corner Threshold：角阈值，当拐角处的角度达到这个阈值时将使用角图片
- Stretch UV：是否拉伸UV，如果勾选纹理的UV将伸展到整个精灵形状的直线上
- Pixel Per Unit：禁用拉伸UV时才有，Unity单位对应多少像素，较高的值会较少纹理的大小
- Worldspace UV：根据世界控件的UV填充纹理

### 启用编辑状态 参数

- Tangent Mode ：切线模式
  - 顶点模式-点两侧不构成曲线
  - 切线模式-点两侧构成曲线，并且可以控制切线弧度
  - 左右切线模式-点两侧构成曲线，并且可以分别控制左右两侧切线弧度
- Position：选中点的局部坐标位置
- Height：控制点左右两侧精灵图片的高度
- Corner：是否使用角度图片
  - Disabled：不使用角度图片
  - Automatic：自动
- Sprite Variant：选择使用的精灵图片
- Snapping：是否开启捕捉设置控制点

