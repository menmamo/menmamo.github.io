---
title: "鼠标公共类（Cusor）"
author: "mona"
date: 2024-06-17T11:40:00+08:00
categories: ["Unity"]
tags: ["Unity输入系统", "UnityEngine"]
---

## 鼠标公共类Cusor

### 隐藏鼠标

```c#
Cusor.visible= false;
```

### 锁定鼠标

编辑器模式按esc摆脱锁定

```c#
//CursorLockMode枚举
//none不锁定 Locked将锁定在屏幕中心点（并隐藏鼠标） Confined限制在窗口范围内
Cursor.lockState = CursorLockMode.Locked;
```

### 设置鼠标图片

- 参数1：光标图片(宽高要一样否则会变形 Texture Type选择Cursor)
- 参数2：偏移位置 相对图片左上角
- 参数3：平台支持的光标模式（硬件或软件）
  - 一般使用自动模式

```c#
Cursor.SetCursor(texture,vector2.zero,CursorMode.Auto);
```