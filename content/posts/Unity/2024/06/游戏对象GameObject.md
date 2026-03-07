---
title: "游戏对象GameObject"
author: "mona"
date: 2024-06-17T11:44:00+08:00
categories: ["Unity"]
tags: ["MonoBehavior"]
---

## GameObject

### GameObject成员变量

 **组件是否激活**

```C#
Debug.Log(gameObject.activeSelf);
```

**查看物体是否静态**

```c#
Debug.Log(gameObject.isStatic);
```

**查看物体所在的层级Layer**

```c#
Debug.Log(gameObject.layer);
```

**查看物体的标签Tag**

```c#
Debug.Log(gameObject.tag);
```

**获取物体的transform**

```c#
gameObject.transfrom;
```

### GameObject成员方法

**设置对象状态**

```c#
gameObject.SetActive(true);
```

**创建自带立方体**

```c#
GameObject.CreatePrimitive(PrimitiveType.Cube); //返回一个GameObject
```

**查找对象**

- 查找单个对象

  - 只能找到激活的对象

  - 如果存在多个满足条件的对象 则随机查找一个对象

  - 可将变量设置为Public 从面板拖 进行关联

```c#
GameObject obj = GameObject.Find("场景中的物体名");//效率比较低会遍历场景中所有的物体
GameObject obj2 = GameObject.FindWithTag("Player");//根据Tag来寻找对象
GameObject obj3 = GameObject.FindGameObjectWithTag("Player");//另一种写法
```

- 查找多个对象

  - 只能找到激活的对象

  - 只能通过Tag寻找多个

```c#
GameObject[] objs = GameObject.FindGameObjectsWithTag("Player");
```

**GameObject父类Object提供的方法（不常用）**

- GameObject继承自Object
- Unity中的Object和C#中的Object是有区别的
  - Unity里面的Object 命名空间在UnityEngine中 是一个自定义类
  - C#中的Object 命名空间是在System中的

```c#
//可以找到场景中挂载的某一个脚本对象
//该方法是GameObject从Object中继承过来的方法
//效率低 不常用
GameObject.FindObjectOfType<类型>();
```

