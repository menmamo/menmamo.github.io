---
title: "MonoBehavior"
author: "mona"
date: 2024-06-17T11:47:00+08:00
categories: ["Unity"]
tags: ["MonoBehavior"]
---

### MonoBehavior基类

1. 继承了Mono才能够挂载到GameObject上（Unity通过反射去判断是否继承自Mono 继承了才可以挂载）
2. 继承了Mono的脚本不可以被new 只可以挂载
3. 继承Mono的脚本 不需要写构造函数，因为我们不会去new他
4. 一个对象上可以挂载多个Mono脚本（如果没有加DisallowMultipleCpomponent特性）
5. 继承Mono的类也可以被多次继承，遵循面向对象继承多态的规则

### 不继承MonoBehavior的类

1. 不继承Mono的类 不能挂载在GameObject上
2. 想怎么写就怎么写 使用时需要自己new
3. 不继承Mono的类一般是单例模式的类（用于管理模块）或者数据结构类（用于存储数据）
4. 不用保留默认的生命周期函数

## MonoBehaviour成员变量

- gameObject该脚本附着的GameObject
- transform该脚本附着对象的transform
- enable 是否激活（控制这个脚本的） 修改物体的激活状态需要使用gameObject.SetActive();

### 常用方法

1. **获取对象上的脚本**

```c#
GetComponent("脚本名") as 类型;//通过脚本名获取
GetComponent(typeof(脚本名)) as 类型;//根据Type获取
GetComponent<类型>();//推荐使用泛型的方式获取
GetComponents<类型>();//得到挂载的多个脚本 返回一个数组
//另一种写法
List<类型> list = new List<类型>();
GetComponents<类型>(list);
//得到子对象挂载的脚本（也会去找自己身上是否挂载该脚本）
GetComponentInChildren<类型>(false);//true失活也找 默认false
GetComponentsInChildren<类型>(false);//true失活也找 默认false 寻找多个返回一个数组
//得到父对象挂载的脚本
GetComponentInParent<类型>();
GetComponentsInParent<类型>();//寻找多个 返回一个数组
```

2. **尝试获取脚本**

```c#
类型 test;
TryGetComponent<类型>(out test); //返回一个bool 可用if判断
```

