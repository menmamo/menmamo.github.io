---
title: "SpriteAtlas 精灵图集"
author: "mona"
date: 2024-06-19T20:41:00+08:00
categories: ["Unity"]
tags: ["Sprite"]
---

## 在Unity中打开自带的打图集功能

- Edit——>Project Setting——>Editor
- Sprite Packer(精灵包装器，可以通过Unity自带图集工具生成图集)
  - Disabled：默认设置，不会打包图集（3D项目默认不打包图集）
  - Enabled For Builds（Legacy Sprite Packer）：Unity仅在构建时打包图集，在编辑模式下不会打包图集
  - Always Enabled（Legacy Sprite Packer）：Unity在构建时打包图集，在编辑模式下运行前会打包图集
  - Enabled For Build：Unity进在构建时打包图集，在编辑器模式下不会打包
  - Always Enabled：Unity在构建时打包图集，在编辑模式下运行前会打包图集

**传统打包模式Legacy Sprite Packer**

相对下面两种模式来说 多了一个设置图片之间的间隔距离

Padding Power:选择打包算法在计算打包的精灵之间以及精灵与生成的图集边缘之间的间隔距离

## 图集面板参数相关

### Type 图集类型

**主图集Master**

- Include in Build 选中可以在当前构建中包含图集
- Allow Rotation 选中此选项将在打包图集时对图片元素进行旋转 可以最大限度的提高组合后的图集密度
  - 注意：如果是UI图集 需要禁用该选项 因为打包时会将UI元素旋转
- Tight Packing 打包图集时实用图片轮廓来打包 而不是矩形 可以最大限度提高组合后的图集密度
- Padding 图集中图片的间隔

**变体图集Variant** 

- Master Atlas 关联的主图集
- Include in Build 选中可以在当前构建中包含图集
- Scale 设置变体图集的缩放因子（0~1）变体图集的大小是主图集乘以Scale的结果

**什么是变体图集？**

变体图集的是以主图集为基础，对他进行缩放产生一个新的图集副本。如果想实用变体图集中的内容，需要勾选变体图集的Include in Build选项 ，而主图集禁用此选项即可

### 代码加载图集

```c#
GameObject obj = new GameObject();
SpriteRenderer sr = obj.AddComponent<SpriteRenderer>();
//加载图集资源
SpriteAtlas spriteAtlas = Resources.Load<SpriteAtlas>("MyAtlas");
//加载图集资源中的某一张小图
sr.sprite = spriteAtlas.GetSprite("dead1");
```

