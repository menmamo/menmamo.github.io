---
title: "RectTransformUtility类"
author: "mona"
date: 2024-07-09T16:40:00+08:00
categories: ["Unity"]
tags: ["UGUI"]
---

## 什么是RectTransformUtility类

RectTransformUtility 公共类是一个RectTransform的辅助类

主要用于进行一些 坐标的转换等等操作

其中对于我们目前来说 最重要的函数是 将屏幕空间上的点，转换成UI本地坐标下的点

## 将屏幕坐标转换为UI本地坐标系下的点

方法：RectTransformUtility.ScreenPointToLocalPointInRectangle

- 参数1：相对父对象
- 参数2：屏幕点
- 参数3：摄像机
- 参数4：最终得到的点

将屏幕上的一个点 转换为UI相对父物体的本地坐标一般配合拖拽事件使用

```c#
public void OnDrag(PointerEventData eventData)
{
    ////一般配合拖拽事件使用
    Vector2 nowPos;
    RectTransformUtility.ScreenPointToLocalPointInRectangle(
    parent,//父对象
    eventData.position,//PointerEventData提供的鼠标在屏幕上的位置
    eventData.enterEventCamera,//可以通过PointerEventData得到触发事件的摄像机
    out nowPos );
    this.transform.localPosition = nowPos;
}
```

