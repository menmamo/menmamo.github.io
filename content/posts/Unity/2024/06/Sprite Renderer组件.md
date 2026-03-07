---
title: "Sprite Renderer组件"
author: "mona"
date: 2024-06-18T20:17:00+08:00
categories: ["Unity"]
tags: ["Sprite"]
---

**Sprite**：渲染的精灵图片

**Color**：定义着色 一般没有特殊需求不会修改

**Filp**：水平或者垂直翻转精灵图片

**Draw Mode** 绘制模式 当尺寸变化时的缩放方式

- Simple 简单模式 缩放时整个图像一起缩放

- Sliced 切片模式 九宫格切片模式

- Tiled 平铺模式  需要把精灵的网络类型设置为Full Rect

  - Continuous：当尺寸变化时 中间部分将均匀平铺

  - Adaptive 当尺寸变化时 雷士Simple模式 当尺寸达到Stretch Value时中间才开始平铺

**Mask Interaction** 与精灵遮罩交互时的方式

- None 不与场景中的任何精灵遮罩交互
- Visible inside Mask 精灵遮罩覆盖的地方可见 而遮罩外部不可见
- Visible Outside Mask 精灵遮罩外部的地方可见 而遮罩覆盖处不可见

**Material**材质：

​	可以使用一些自定义材质来显示一些特殊效果

​	一般情况不修改，默认材质是不会受到光照影响的，如果想要受到光照影响 可以选择Default-Diffuse

**Additional Settring** 高级设置

- Sorting Layer 排序层选择
- Order in Layer 层级序列号 数值大的后渲染（显示在前面）

### 通过Resources加载Sprite

```c#
private Dictionary<string, Dictionary<string, Sprite>> dic = new ();
var sprs = Resources.LoadAll<Sprite>(multipleName);
for (int i = 0; i < sprs.Length; i++)
{
    dicTmp.Add(sprs[i].name, sprs[i]);
}
```

