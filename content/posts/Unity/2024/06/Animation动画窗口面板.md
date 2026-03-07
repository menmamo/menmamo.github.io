---
title: "Animation动画窗口面板"
author: "mona"
date: 2024-06-24T14:19:00+08:00
categories: ["Unity"]
tags: ["动画系统"]
---

## Animation窗口是用来干啥的

- Animation窗口 直译就是动画窗口
- 它主要用于在Unity内部创建和修改动画
- 所有在场景中的对象都可以通过Animation窗口为其制作动画

## 打开Animation窗口

Window——>Animation——>Animation

## 动画时间轴

- 每一个动画文件都有自己的一个生命周期，从动画开始到结束
- 我们可以在动画时间轴上编辑每一个动画生命周期中变化

## 动画中的帧

- 假设某个动画的帧率为60帧每秒，意味着该动画1秒钟最多会有60次改变机会
- 每一帧的间隔时间是 1s/60 ≈ 16.67毫秒 也就是说 我们最快可以每16.67毫秒改变一次对象状态

## 关键帧

动画在时间轴上的某一个时间节点上处于的状态

## Animation窗口功能

![](/usr/uploads/2024/06/image-20240622131237935.png)

- 预览模式（开关）
- 录制模式（开关）打开后预览模式默认开始
- 回到动画开头
- 上一关键帧
- 播放动画）
- 下一关键帧
- 回到动画结尾
- 当前处于第几帧

![](/usr/uploads/2024/06/image-20240622131457215.png)

- Dopesheet关键帧模式
- Curves曲线模式

![](/usr/uploads/2024/06/image-20240622131527520.png)

- 按选择筛选
- 添加关键帧
- 添加事件

## 创建动画

1. 在场景中选中想要创建动画的对象
2. 在Animation窗口中点击创建
3. 选择动画文件将要保存到的位置

保存动画文件时，Unity会帮助我们完成以下操作

1. 创建一个 Animator Controller（动画控制器或称之为动画状态机） 资源（新动画系统）
2. 将新创建的动画文件添加到Animator Controller中
3. 为动画对象添加Animator组件
4. 为Animator组件关联创建的Animator Controller文件

### 面板变化

![](/usr/uploads/2024/06/image-20240622131808199.png)
可以在这里创建新动画和切换动画

![](/usr/uploads/2024/06/image-20240622131816067.png)

多出一个可以添加变化属性的按钮
在这里你可以任意添加你想要在动画中变化的脚本变量

![](/usr/uploads/2024/06/image-20240622131833405.png)

- Add Key：添加关键帧
- Delete Key：删除关键帧
- Clamped Auto：切线模式，自动设置切线
- Auto：旧版切线模式，和Clamped Auto基本类似，主要用于老动画系统，建议不使用除非是老动画
- Free Smooth：自由设置切线
  Flat：切线设置为水平
- Broken：单独控制左右曲线切线
  - Free：自由设置
  - Linear：直线
  - Constant：常量切换
  - Weighted：权重切换

### 动画文件界面参数

![](/usr/uploads/2024/06/image-20240622131949673.png)

- Loop Time：是否是循环动画（在动画结尾时重新开始）
- Loop Pose：无缝循环运动
- Cycle Offset：循环动画在其他时间开始时的周期偏移

