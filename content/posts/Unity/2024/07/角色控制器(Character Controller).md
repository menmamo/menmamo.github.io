---
title: "角色控制器(Character Controller)"
author: "mona"
date: 2024-07-02T16:49:00+08:00
categories: ["Unity"]
tags: ["Unity基础组件"]
---

## 角色控制器是什么？

角色控制器是让角色可以受制于碰撞，但是不会被刚体所牵制

如果我们对角色使用刚体判断碰撞，可能会出现一些奇怪的表现

比如：

1. 在斜坡上往下滑动
2. 不加约束的情况碰撞可能让自己被撞飞等等

而角色控制器会让角色表现的更加稳定

Unity提供了角色控制器脚本专门用于控制角色

**注意**

- 添加角色控制器后，不用再添加刚体
- 能检测碰撞函数
- 能检测触发器函数
- 能被射线检测

## 角色控制器的使用

1. 为对象添加角色控制器组件：Character Controller
2. 使用代码控制

## 角色控制器参数

![](/usr/uploads/2024/07/image-20240702164419422.png)

- **Slope Limit** 

  坡度度数限制，大于该值的斜坡上不去

- **Step Offset**

  台阶偏移值，单位为米，低于这个值的台阶才能上去，该值不能大于角色控制器的高度

- **Skin Width**

  皮肤的宽度，两个碰撞器可以穿透彼此最大的皮肤宽度

  较大的值可以减少抖动，较小的值可能会导致角色卡住

  建议设置为半径的10%

- **MinMoveDistance**

  最小的移动距离

  一般设置为0，可以用来减少抖动

## 代码相关

**获得角色控制器**

```c#
var cc = GetComponent<CharacterController>();
```

**判断是否接触到地面**

```c#
if ( cc.isGrounded )
{
    print("接触地面了");
}
```

**移动**

```c#
//受重力作用的移动
cc.SimpleMove(Vector3.forward * 10 * Time.deltaTime);
//不受重力作用的移动
cc.Move(Vector3.forward * 10 * Time.deltaTime);
```

**角色控制器 生命周期函数**

```c#
//当角色控制器想要判断和别的碰撞器产生碰撞时 使用该函数
private void OnControllerColliderHit(ControllerColliderHit hit)
{
    print(hit.collider.gameObject.name);
}
//可以检测触发器
private void OnTriggerEnter(Collider other)
{
    print("触发器触发");
}
//OnCollisionEnter对角色控制器没用
```

