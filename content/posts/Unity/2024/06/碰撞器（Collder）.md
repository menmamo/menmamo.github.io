---
title: "碰撞器（Collder）"
author: "mona"
date: 2024-06-17T11:39:00+08:00
categories: ["Unity"]
tags: ["物理系统", "MonoBehavior"]
---

## 碰撞产生的条件

两个物体都要有碰撞器 至少有一个物体有刚体组件

------

## 什么是碰撞器

碰撞器表示物体的体积（形状） 刚体会利用体积进行碰撞计算，模拟真实的碰撞效果，产生力的作用

------

## 3D碰撞器种类

- 盒状
- 球状
- 胶囊
- 网格
- 轮胎
- 地形

------

## 通用参数

- Is Trigger是否是触发器 如果启用此属性 则碰撞体将用于触发事件 并被物理引擎忽略
- Material 物理材质 可以确定碰撞体和其他对象碰撞时的行为
- Center 碰撞体在对象局部空间中的中心点位置

------

## 常用碰撞器

**Box Collider 盒状碰撞器** 

- Size 碰撞体在X、Y、Z方向上的大小

**Sphere Collider 球状碰撞器**

- Radius 碰撞体的半径大小

**Capsule Collider 胶囊碰撞器**

- Radius 胶囊的半径
- Height 胶囊的高度
- Direction胶囊在对象局部空间中的轴向

------

## 异形物体使用多种碰撞器组合

父对象添加刚体 子对象没有刚体也会参与碰撞检测（作为一个整体）

------

## 不常用的碰撞器

**Mesh Collider网格碰撞器 性能消耗较高**

- Convex 启用此属性 该Mesh Collider将和其他Mesh Collider发生碰撞 Convex Mesh Collider最多255个三角形 如果启用了刚体就要启用了该属性 才产生力的作用
- Cooking Options 启用或金禁用影响物理引擎对网络处理方式的网络烹制选项
- Mesh 引用需要用于碰撞的网络

**Wheel Collider环状碰撞器 轮胎碰撞器**

- Mass车轮的质量
- Radius车轮半径
- Whell Damping Rate 车轮的阻尼值
- Suspension Distance 车轮悬架的最大延申距离
- Force App Point Distance 车轮上的受力点
- Suspension Spring 悬架尝试通过增加弹簧力和阻尼力来达到目标位置
- Forward Friction 车轮向前滚动时轮胎摩擦的特性
- Sideways Friction 侧向滚动时的轮胎摩擦的特性

**Terrain Collider地形碰撞器 性能消耗较高**

- Terrain Data地形数据
- Enable Tree Colliders 选中启用树碰撞体

------

## 碰撞检测函数

碰撞检测函数是Unity通过反射来调用的，在FixedUpdate和Update之间调用
碰撞检测的频率和物理帧的更新相关
如果是刚体在父对象上 碰撞盒在子对象上，则需要把脚本挂载到父对象上 挂载到子对象上是检测不到的
一般会把触发和碰撞函数写成私有或保护类型的 因为unity会使用反射来调用这些函数
碰撞检测函数是可以写成虚方法被子类重写的

```c#
//碰撞触发解除时执行  触发器：OnTriggerEnter
private void OnCollisionEnter(Collision collision){}
//碰撞结束分离时执行  触发器：OnTriggerExit
private void OnCollisionExit(Collision collision){}
//解除摩擦时 会调用这个函数 触发器：OnTriggerStay
private void OnCollisionStay(Collision collision){}
```

**Collision参数**

```c#
//碰撞到的对象碰撞器的信息
collision.collider
//碰撞对象依附的对象
collision.gameobject
//碰撞对象的依附对象的位置信息
collision.tranform
//触碰点数相关
collision.contactCount//具体解除了几个点
//触碰点具体的坐标信息
ContactPoint[] pos = collision.contacts;
```