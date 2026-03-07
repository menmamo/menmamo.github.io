---
title: "SpriteResolver换装"
author: "mona"
date: 2024-06-26T20:19:00+08:00
categories: ["Unity"]
tags: ["2D骨骼动画"]
---

## 如何在同一个psb文件中制作换装资源

1. 在ps中制作美术资源时，将一个游戏对象的所有换装资源都摆放好位置
2. 当我们导入该资源时，要注意是否导入隐藏的图层

##  编辑换装资源的骨骼信息以及分组类别

1. 每个部位 关联的骨骼要明确设置
2. 为同一个部位的不同装备分组

## 如何换装

### 两个关键组件

- SpriteLibrary——精灵资料库，确定类别分组信息
- SpriteResolver——精灵解算器，用于确定部位类别和使用的图片

### 一个数据文件

- SpriteLibraryAsset——精灵资料库资源，具体记录类别分组信息的文件

1. 创建SpriteLibraryAsset 将精灵文件拖入
2. 在父对象添加SpriteLibrary组件 并和SpriteLibraryAsset关联
3. 找到需要换装的Sprite子物体 挂载SpriteResolver组件

## 代码控制换装

### 获取SpriteResolver 

```c#
public SpriteResolver sr;
sr = GetComponent<SpriteResolver>();
```

### 获取当前部位默认的类别名

```c#
sr.GetCategory();
```

### 设置当前部位想要切换的图片信息

```c#
sr.SetCategoryAndLabel(sr.GetCategory(), "CASK 1");
```

