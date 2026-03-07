---
title: "EventSystem和StandaloneInputModule"
author: "mona"
date: 2024-07-05T14:02:00+08:00
categories: ["Unity"]
tags: ["UGUI"]
---

## Event System组件用来干啥

Event System意思是事件系统

它是用于管理玩家的输入事件并分发给各UI控件 

它是事件逻辑处理模块 所有的UI事件都通过EventSystem组件中轮询检测并做相应的执行它类似一个中转站，和许多模块一起共同协作

如果没有它，所有点击、拖曳等等行为都不会被响应

## Event System组件参数

- First Selected

  首先选择的游戏对象，可以设置游戏一开始的默认选择

- Send Navigation Events

  是否允许导航事件（移动/按下/取消）

- Drag Threshold

  拖拽操作的阈值（移动多少像素算拖拽）

## Standalone Input Module组件用来干啥的

Standalone Input Module意思是 

独立输入模块 

它主要针对 

处理鼠标/键盘/控制器/触屏（新版Unity）的输入输入的事件通过EventSystem进行分发

它依赖于EventSystem组件，他们两缺一不可

## Standalone Input Module组件参数

- **Horizontal Axis**

  水平轴按钮对应的热键名（该名字对应Input管理器）

- **Vertical Axis**

  垂直轴按钮对应的热键名（该名字对应Input管理器） 

- **Submit Button**

  提交（确定）按钮对应的热建名（该名字对应Input管理器）

- **Cancel Button**

  取消按钮对应的热建名（该名字对应Input管理器） 

- **Input Actions Per Second**

  每秒允许键盘/控制器输入的数量 

- **Repeat Delay**

  每秒输入操作重复率生效前的延迟时间 

- **ForceModule Active**

  是否强制模块处于激活状态

EventSystem和Standalone Input Module组件他们主要是用于输入事件监听管理和分发的 如果没有了他们两 那么UI中的所有输入事件将不会被响应