---
title: "GrabPass命令"
author: "mona"
date: 2025-06-19T11:04:25+08:00
categories: ["Unity"]
tags: ["shader"]
---

我们可以利用GrabPass命令把即将绘制对象时的屏幕内容抓取到纹理中

在后续通道中即可使用此纹理，从而执行基于图像的高级效果。

举例：

将绘制该对象之前的屏幕抓取到 _BackgroundTexture 中

```
GrabPass
{
    "_BackgroundTexture"
}
```

注意：

该命令一般写在某个Pass前，在之后的Pass代码中可以利用_BackgroundTexture变量进行处理

