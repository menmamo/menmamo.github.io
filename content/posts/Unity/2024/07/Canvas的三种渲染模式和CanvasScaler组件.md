---
title: "Canvas的三种渲染模式和CanvasScaler组件"
author: "mona"
date: 2024-07-05T11:40:00+08:00
categories: ["Unity"]
tags: ["UGUI"]
---

## Canvas是什么？

Canvas的意思是画布
他是UGUI中所有UI元素能够被显示的根本
他主要负责渲染自己所有的子对象

------

## Canvas的三种渲染模式

**Screen Space - Overlay**
屏幕空间，覆盖模式，UI始终在前

**Screen Space - Camera**
屏幕空间，摄像机模式，3D物体可以显示在UI前面

**World Space**
世界空间，3D模式

------

### Screen Space - Overlay

覆盖模式，UI始终显示在场景内容前方
![](/usr/uploads/2024/10/4064492085.png)

**Pixel Prefect**
是否开启无锯齿精确渲染（性能换效果）

**SortOrder**
排序层编号（用于控制多个Canvas时的渲染先后顺序）

**TargetDisplay**
目标设备（在哪个显示设备上显示）

**Additonal Shader Channels**
其他着色器通道们，决定着色器可以预读取那些数据

------

### Screen Space - Camera

3D物体可以显示在UI前面

**RenderCamera**
用于渲染UI的摄像机（如果不设置将类似于覆盖模式）

**Plane Distance**
UI平面在摄像机前方的距离，类似整体Z轴的感觉

**Sorting Layer**
所在排序层

**Order in Layer**
排序层的序号

------

### World Space

3D模式，把UI对象像3D物体一样处理，一般用于VR或者AR

**EventCamera**
用于处理UI事件的摄像机
如果不设置不能注册事件，一般设置为主摄像机

------

## CanvasScaler组件是什么？

CanvasScaler意思是画布缩放控制器
它是用于分辨率自适应的组件
CanvasScaler提供了三种用于分辨率自适应的模式 我们可以根据需求来选择

------

## Constant Pixel Size（恒定像素模式）

无论屏幕大小如何,，UI始终保持相同像素大小

**Scale Factor**
缩放系数，按此系数缩放画布中所有的UI元素

**Refrerence Pixels Pre Unit**
单位参考像素，多少像素对应Unity中的一个单位
默认100个像素对应一个单位
图片设置中的Pixel Per Unit设置 会和该参数一起参与计算

**恒定像素模式公式**
UI原始尺寸 = 图片大小(像素) / (Pixel Per Unit / Refrerence Pixels Pre Unit)

------

## Scale With Screen Size(缩放模式)

根据屏幕尺寸进行缩放，随着屏幕尺寸变化来放大缩小

**Reference Resolution**
参考分辨率（美术同学出图的标准分辨率）。
缩放模式下的所有匹配模式都会基于参考分辨率进行自适应计算

**Screen Match Mode**
屏幕匹配模式，当前屏幕分辨率宽高比不适应参考分辨率时，用于分辨率大小自适应的匹配模式

- Expand：水平或垂直拓展画布区域，会根据宽高比的变化来放大缩小画布，可能有黑边
- Shrink：水平或垂直裁剪画布区域，会根据宽高比的变化来放大缩小画布，可能会裁剪
- Match Width Or Height：以宽高或者二者的平均值作为参考来缩放画布区域

------

## 屏幕匹配模式

### Expand拓展匹配

将Canvas Size进行宽或高扩大，让他高于参考分辨率

**计算公式**：

缩放系数 = Mathf.Min(屏幕宽/参考分辨率宽，屏幕高/参考分辨率高);
画布尺寸 = 屏幕尺寸 / 缩放系数

**表现效果**
最大程度的缩小UI元素，保留UI控件所有细节，可能会留黑边

------

### Shrink收缩匹配

将Canvas Size进行宽或高收缩，让他低于参考分辨率

**计算公式**： 
缩放系数 = Mathf.Max(屏幕宽/参考分辨率宽，屏幕高/参考分辨率高); 
画布尺寸 = 屏幕尺寸 / 缩放系数

**表现效果**
最大程度的放大UI元素，让UI元素能够填满画面，可能会出现裁剪

------

### Match Width Or Height宽高匹配

以宽高或者二者的某种平均值作为参考来缩放画布
Match：确定用于计算的宽高匹配值

**计算公式描述**

```c#
//在取平均值之前，我们先取相对宽度和高度的对数
float logWidth = Mathf.Log(屏幕宽 / 参考分辨率宽, 2);
float logHeight = Mathf.Log(屏幕高 / 参考分辨率高, 2);
//在对数空间中变换是为了获得更好的性能以及更准确的结果
float logWeightedAverage = Mathf.Lerp(logWidth, logHeight, m_MatchWidthOrHeight);
scaleFactor = Mathf.Pow(2, logWeightedAverage);
```

**表现效果**

主要用于只有横屏模式或者竖屏模式的游戏

**竖屏游戏**
Match = 0
将画布宽度设置为参考分辨率的宽度 并保持比例不变，屏幕越高可能会有黑边

**横屏游戏**
Match = 1
将画布高度设置为参考分辨率的高度 并保持比例不变，屏幕越长可能会有黑边

------

### 屏幕匹配模式的选择

游戏开发一般使用 

Scale With Screen Size 缩放模式 

使用建议 

存在横竖屏切换选择：

Expand（拓展匹配，有黑边）和 Shrink（收缩匹配，有裁剪）

不存在横竖屏切换（定死的横屏或者竖屏游戏）：

Match Width or Height（宽高匹配） 

横屏游戏 Match = 1
竖屏游戏 Match = 0

------

## Constant Physical Size（恒定物理模式）

无论屏幕大小和分辨率如何，UI元素始终保持相同物理大小
**DPI**（Dots Per Inch每英寸像素点数）图像每英寸长度内的像素点数
**Physial Unit**
物理单位，使用的物理单位种类

 - Centimeters厘米（2.54厘米 对应一英寸）
 - Millimeters毫米（25.4毫米 对应一英寸）
 - Inches英寸
 - Points点（72）
 - Picas皮卡（6）

**FallBack Screen DPI**
备用DPI，当找不到DPI设备时，使用此值

**Default Sprite DPI**
默认图片DPI

**计算公式** 
根据DPI算出新的 
Reference Pixels Per Unit （单位参考像素） 
新单位参考像素 = 单位参考像素 * Physical Unit / Default Sprite DPI

------

### 恒定像素模式和恒定物理模式区别

相同点：他们都不会进行缩放，图片有多大显示多大，使用他们不会进行分辨率大小自适应

不同点：相同尺寸不同DPI设备像素点区别，像素点越多细节越多同样为5像素，DPI较低的设备上看起来的尺寸可能会大于DPI较高的设备

恒定物理模式 它不会让UI控件进行分辨率大小自适应 会让UI控件始终保持设置的尺寸大小显示 而且会根据设备DPI进行计算，让在不同设备上的显示大小更加准确一般在进行游戏开发极少使用这种模式

------

## World 世界模式（简称：3D模式）

Dynamic Pixels Per Unit：UI中动态创建的位图（例如文本）中，单位像素数（类似密度）Reference Pixels Per Unit：单位参考像素，多少像素对应Unity中的一个单位（默认一个单位为100像素）

只有在3D渲染模式下才会启用的模式 主要用于控制该模式下的像素密度