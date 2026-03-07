---
title: "动画IK控制"
author: "mona"
date: 2024-07-01T21:10:00+08:00
categories: ["Unity"]
tags: ["骨骼动画"]
---

## 什么是IK？

- 在骨骼动画中，构建骨骼的方法被称为正向动力学
- 它的表现形式是，子骨骼（关节）的位置根据父骨骼（关节）的旋转而改变
- 当我们抬起手臂时，是肩部关节带动的整个手臂的运动，用父子骨骼理解的话就是父带动了子
- 而IK全称是Inverse Kinematics，翻译过来的意思就是反向动力学的意思
- 它的表现形式是，子骨骼（关节）末端的位置改变会带动自己以及自己的父骨骼（关节）旋转

## 如何进行IK控制

1. 在状态机的层级设置中 开启 IK 通道

2. 继承MonoBehavior的类中

   Unity定义了一个IK回调函数:OnAnimatorIK

   我们可以在该函数中调用Unity提供的IK相关API来控制IK

## Animator中的IK相关API

**OnAnimatorIK回调函数**

```c#
//参数 状态机层级
private void OnAnimatorIK(int layerIndex)
{
	//设置ik的代码
}
```

**设置头部IK权重**

```c#
//重载
//weight:LookAt全局权重0~1
//bodyWeight:LookAt时身体的权重0~1
//headWeight:LookAt时头部的权重0~1
//eyesWeight:LookAt时眼镜的权重0~1
//clampWeight:0表示角色运动时不受限制，1表示角色完全固定无法执行LookAt，0.5表示只能够移动范围的一半
animator.SetLookAtWeight(1f,1f,1f);
```

**设置头部IK看向位置**

```c#
animator.SetLookAtPosition(pos.position);
```

**设置IK位置权重**

```c#
//参数1：AvatarIKGoal枚举类 设置ik关节
//参数2：权重
animator.SetIKPositionWeight(AvatarIKGoal.RightFoot, 1f);
```

**设置IK旋转权重**

```c#
//参数1：AvatarIKGoal枚举类 设置ik关节
//参数2：权重
animator.SetIKRotationWeight(AvatarIKGoal.RightFoot, 1f);
```

**设置IK对应的位置**

```c#
//参数1：AvatarIKGoal枚举类 设置ik关节
//参数2：位置
animator.SetIKPosition(AvatarIKGoal.RightFoot, pos2.position);
```

**设置IK对应的角度**

```c#
//参数1：AvatarIKGoal枚举类 设置ik关节
//参数2：角度
animator.SetIKRotation(AvatarIKGoal.RightFoot, pos2.rotation);
```

