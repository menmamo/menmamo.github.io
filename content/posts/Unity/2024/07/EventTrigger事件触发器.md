---
title: "EventTrigger事件触发器"
author: "mona"
date: 2024-07-09T14:18:00+08:00
categories: ["Unity"]
tags: ["UGUI"]
---

## 事件触发器是什么

事件触发器是EventTrigger组件

它是一个集成了上节课中学习的所有事件接口的脚本

它可以让我们更方便的为控件添加事件监听

## 如何使用事件触发器

1. 拖曳脚本进行关联
2. 代码添加

## 代码添加

```c#
//申明一个希望监听的事件对象
EventTrigger.Entry entry = new EventTrigger.Entry();
//申明 事件的类型
entry.eventID = EventTriggerType.Drag;
//监听函数关联
entry.callback.AddListener((data) =>
{
    print("抬起");
});
//把申明好的 事件对象 加入到 EventTrigger当中
et.triggers.Add(entry);
```

