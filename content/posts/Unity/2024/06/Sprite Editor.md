---
title: "Sprite Editor"
author: "mona"
date: 2024-06-18T14:14:00+08:00
categories: ["Unity"]
tags: ["Sprite"]
---

- SpriteEditor就是 精灵图片编辑器，它主要用于编辑2D游戏开发中使用的Sprite精灵图片，可以编辑 图集中提取元素，设置精灵边框，设置九宫格，设置轴心（中心）点等等
- 新版本Unity 需要安装 2D Sprite包才能使用SpriteEditor

### Sprite Editor 窗口按钮

#### 左下角设置窗口

![](/usr/uploads/2024/06/image-20240618141022488.png)

- Name：名字
- Position 在图片中的偏移位置和宽高
- Border 边框 用于九宫格切图的四条边
- Pivot 轴心 中心点位置
- Pivot Unit Mode：轴心点单位模式
  - Normalizd 标准化模式0~1
  - Pixels 像素模式
- Custom Pivot 自定义轴心点

#### 顶部设置按钮

![](/usr/uploads/2024/06/image-20240618141207686.png)

- Snap：将控制点贴近在最近的像素
- Outline Tolerance 轮廓点的复杂性和准确性 值越大 轮廓点越多越准确
- Generate 生成网络轮廓

### Custom Outline（决定渲染区域）

- 自定义边缘线设置，可以自定义精灵网格的轮廓形状
- 默认情况下不修改都是在矩形网格上渲染,边缘外部透明区域会被渲染，浪费性能
- 使用自定义轮廓，可以调小透明区域，提高性能

### Custom Physics Shape（决定碰撞判断区域）

- 自定义精灵图片的物理形状，主要用于设置需要物理碰撞判断的2D图形
- 它决定了之后产生碰撞检测的区域

### Secondary Textures(为图片添加特殊效果)

- 次要纹理设置，可以将其它纹理和该精灵图片关联
- 着色器可以得到这些辅助纹理然后用于做一些效果处理
- 让精灵应用其它效果

## Multiple图集模式

- 当我们的图片资源是图集时
- 我们需要在设置时将模式设置为Multiple
- 这时我们可以使用Sprite Editor自带的功能进行图集元素分割

### 自动分割

- Pivot 单张图片轴心点位置
- Custom Pivot 自定义轴心点
- Method 如何处理现有对象
  - Delete Exiting 替换掉已经选择的任何矩形
  - Smart 尝试创建新的矩形 同时保留或者调整现有矩形
  - Safe 添加新的矩形而不是更改任何已经存在的矩形

### 按单元格大小分割

- PixelSize 单元格宽高
- Offset：偏移位置
- Padding 和边缘的偏移位置
- Keep Empty Rects 是否保留空矩形

### 平分切割

Column&row：行列数

## Polygon图片编辑

多边形 不常用

Sides设置多少个边