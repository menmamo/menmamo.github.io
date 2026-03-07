---
title: "Rigidbody2D使用与优化"
author: "mona"
date: 2024-06-29T10:57:00+08:00
categories: ["Unity"]
tags: ["2D物理"]
---

## 刚体的移动

当gameobject被加上rigidbody2d组件时，如果想移动它，不要这样写：

```c#
void Update(){
        this.transform.Translate (Vector3.left * speed * Time.deltaTime);
}
```

这种写法，主要针对无rigidbody2d的gameobject对象，否则，性能很差。
正确的写法：

方法一：

```c#
void FixedUpdate()
{
    rigidbody2D.MovePosition (rigidbody2D.position + Vector2.left * speed * Time.fixedDeltaTime);
}
```

方法二：

```c#
void Start () 
{
    rigidbody2D.velocity = Vector2.left * speed;
}
```

## Body Type属性

rigidbody2d最为重要的一个属性，不同的选项，不同的物理效果：

### Dynamic（动态的）

1. 具有完全的物理特性
2. 会与所有类型的Rigidbody2D进行碰撞；
3. 是最常用的Rigidbody2D类型、也是默认的类型；
4. 是最耗费性能的类型；

### Kinematic（运动学）

1. 仅在用户控制下运动，不会受到重力和AddForce等力相关函数的影响;
2. 只与Dynamic的Rigidbody2D发生碰撞（不勾选Use Full Kinematic Contacts）；
3. 不能通过力或碰撞改变速度，但是可以设置其速度和位置、旋转；
4. 比Dynamic性能高；

### Static (静态的)

1. 具有无限质量、不可移动的物体;
2. velocity、AddForce、gravity、MovePosition、MoveRotation都不可用;
3. 只与Dynamic的Rigidbody2D发生碰撞;
4. 性能最高；

## Simulated 物理模拟

设置为true（勾选）时开启模拟；设置为false（不勾选）时关闭模拟。

模拟包括：

1. 运动
2. Collider2D的碰撞
3. Joint2D的约束效果
4. 是否驻留在内存

直接更改此属性，比直接启用/禁用Collider2D组件或Joint2D组件更有高效；

## Use Full Kinematic Contacts

Kinematic 模式下的属性。

- 勾选后，在碰撞上类似于Dynamic Rigidbody2D，可以和所有类型Rigidbody2D进行碰撞，但是仍然不会受到力的影响；
- 不勾选，只会与Dynamic Rigidbody2D发生碰撞；

## 其他注意事项：

1. 运行中，不要修改Rigidbody2D的Body Type属性，性能会变差。

2. 如果一个Rigidbody2D需要移动，并且接受完全的物理模拟，包括重力、碰撞、施加力等，

   那么需要将Rigidbody2D设置成Dynamic，并附加Collider2D组件。

3. 如果一个Rigidbody2D需要移动，但不接受力的作用，那么需要将它设置成Kinematic；

4. 如果一个Rigidbody2D不需要移动，也不需要接收力的作用，但是需要接受碰撞，
   那么需要将Rigidbody2D设置为Static，并附加Collider2D组件。