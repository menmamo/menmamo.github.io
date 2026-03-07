---
title: "Animation老动画系统"
author: "mona"
date: 2024-06-24T14:20:00+08:00
categories: ["Unity"]
tags: ["动画系统"]
---

## 老动画系统

### 什么是老动画系统

- Unity中有两套动画系统
- 新：Mecanim动画系统——主要用Animator组件控制动画
- 老：Animation动画系统——主要用Animation组件控制动画（Unity4之前的版本可能会用到）
- 目前我们为对象在Animation窗口创建的动画都会被新动画系统支配
- 有特殊需求或者针对一些简易动画，才会使用老动画系统

### Animation组件



![](/usr/uploads/2024/06/image-20240622132044039.png)

- Animation：默认播放的动画
- Animations：该动画组件可以控制的所有动画
- Play AutoMatically：是否一开始就自动播放默认动画
- Animate Physics：动画是否与物理交互
- Culling Type：决定什么时候不播放动画
  - Always Animate：始终播放
  - Based On Renderers：基于默认动画姿势剔除

![](/usr/uploads/2024/06/image-20240622132118118.png)

- Default：读取设置得更高的默认重复模式
- Once：播放一次就停止
- Loop：从头到尾不停循环播放
- PingPong：从头到尾从尾到头不停播放
- ClampForever：播放结束会停在最后一帧，并且会一直播放最后一帧（相当于状态不停止），表现效果和Once一样，但是逻辑处理上不同

### 代码控制动画播放

**获取动画组件**

```c#
var animation = this.GetComponent<Animation>();
```

**播放动画**

```c#
animation.Play("1");
//淡入播放
animation.CrossFade("3");
```

**前一个播完再播放下一个**

```c#
animation.PlayQueued("2");
//淡入播放
animation.CrossFadeQueued("2");
```

**停止播放所有动画**

```c#
animation.Stop();
```

**是否在播放某个动画**

```c#
animation.IsPlaying("1");
```

**播放模式设置**

```c#
animation.wrapMode = WrapMode.Loop;
```

**其它**

```c#
//层级和权重以及混合（老动画系统需要通过代码来达到动画的遮罩、融合等效果）
//设置层级
animation["1"].layer = 1;
//设置权重
animation["1"].weight = 1;
//混合模式 叠加还是混合
animation["1"].blendMode = AnimationBlendMode.Additive;
//设置混组相关骨骼信息
animation[""].AddMixingTransform();
```

