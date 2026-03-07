---
title: "Graphic Raycaster组件"
author: "mona"
date: 2024-07-05T13:44:00+08:00
categories: ["Unity"]
tags: ["UGUI"]
---

## Graphic Raycaster用来干啥

Graphic Raycaster意思是**图形射线投射器**

用于**检测UI输入事件**的射线发射器

它主要负责通过射线检测玩家和UI元素的交互判断是否点击到了UI元素

## Graphic Raycaster参数

- **Ignore Reversed Graphics**

  是否忽略反转图形

- **Blocking Objects**

  射线被哪些类型的碰撞器阻挡（在覆盖渲染模式下无效）

- **Blocking Mask**

  射线被哪些层级的碰撞器阻挡（在覆盖渲染模式下无效）