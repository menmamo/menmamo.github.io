---
title: "屏幕（Screen）"
author: "mona"
date: 2024-06-17T11:39:00+08:00
categories: ["Unity"]
tags: ["UnityEngine"]
---

### 获取屏幕当前分辨率

```c#
//获取显示器分辨率和刷新率
Resolution r = Screen.currentResolution;
//获取游戏窗口的宽高
Screen.width;
Screen.height;
```

### 屏幕休眠模式

```c#
Screen.SleepTimeout = SleepTiomeout.NeverSleep;
```

### 不常用

```c#
//运行时是否全屏模式
Screen.fullScreen = true;
//窗口模式
//FullScreenMode.ExclusiveFullScreen独占全屏 Windowed 窗口模式
Screen.fullScreenMode = FullScreenMode.ExclusiveFullScreen;

//移动设备屏幕旋转相关
Screen.autorotateToLandscapeRight = true;//允许自动旋转为右横向
Screen.autorotateToLandscapeLeft = true;//允许自动旋转为左横向
Screen.autorotateToPortraitUpsideDown = true;//允许屏幕自动旋转为倒着
Screen.autorotateToPortrait = true;//允许自动旋转屏幕为正向

//指定屏幕显示方向
Screen.orientation = ScreenOrientation.LandscapeLeft;//指定为横屏
```

### 设置分辨率 不常用

```c#
Screen.SetResolution(1920,1080,true);//前面两个参数分辨率 第三个参数 是否全屏
```

