---
title: "使用Fungus插件快速实现对话系统"
author: "mona"
date: 2019-11-21T21:20:00+08:00
categories: ["Unity"]
tags: []
---

官方文档：http://fungusdocs.snozbot.com/

官网：http://fungusgames.com/

## 1.安装Fungus

进入unity资源商店下载并导入到项目中

![](/usr/uploads/2019/11/QQ20191121-204437@2x.png)

成功导入插件后，unity的标题栏会多出来一个tools

![](/usr/uploads/2019/11/14C859EA-D6FF-41B3-BDE2-BF412E34DFA7.jpg)

## 2.使用

首先在新建一个Flowchart,此时在Hiearchy面板中会多出来一个Flowchart组件



接着把Flowchart Window窗口显示出来

![](/usr/uploads/2019/11/916CB74E-B91B-4F99-A771-6CBEB584F7B2.jpg)

这时候会出现一个类似动画状态机的东西，我们点击New Block

![](/usr/uploads/2019/11/0D5C279E-88FD-43CE-A1F7-B17F4C7B6037.jpg)

![](/usr/uploads/2019/11/QQ20191121-205844@2x.png)

块名（Block Name）也就是这段对话的名字

自定义颜色（Custom Tint）

块描述（Description）这段对话的备注

重点是下面的Execute On Event属性

这里控制对话的触发事件 默认的状态是Game Started也就是在游戏场景启动时触发对话 并不是游戏开始时触发

![](/usr/uploads/2019/11/QQ20191121-210750@2x.png)

最下面的Wait For Frames则是等待多少帧后触发对话

选择“+”，然后选择narrative里的say，添加一个新的指令。

![](/usr/uploads/2019/11/QQ20191121-210957@2x.png)

![](/usr/uploads/2019/11/QQ20191121-211507@2x.png)



添加的指令会显示在Commands中，会从上到下执行

Say指令里有可调节的参数

例如Character中可以指定让这句话由谁来说

Sey Sya Dialog则是这个对话框的样式

接下来运行游戏，出现了hello word对话框



![](/usr/uploads/2019/11/QQ20191121-211916@2x.png)