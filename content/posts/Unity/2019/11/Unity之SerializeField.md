---
title: "Unity之SerializeField"
author: "mona"
date: 2019-11-18T10:42:00+08:00
categories: ["Unity"]
tags: []
---

 **[HideInInspector]**表示将原本显示在面板上的序列化值隐藏起来。

 **[SerializeField]**表示将原本不会被序列化的私有变量和保护变量变成可以被序列化的，那么它们在下次读取的值就是你上次赋值的值。

## 1、如果a是公有的序列化变量。

**（1）如果想要在面板中看到变量a，那么用：**

```c#
 public int a;
```

**（2）如果不想在面板中看到变量a，那么用：**

```c#
[HideInInspector] public int a;
```

##  2、如果a是私有的序列化变量

**(1)如果想在面板中读取并赋值，那么用:**

```c#
 [SerializeField] private int a;
```

**(2)想在面板中读取，但是不赋值，那么用：**

```c#
  [HideInInspector][SerializedField] private int a;
  public int b{ get{return a;} }
```

然后在Editor中显示，EditorGUILayout.LabelField("value",game.b.ToString());

**(3)如果不想在面板中做任何操作(不想看到，也不想写)，但是想要在程序中给它赋值，那么用**

```c#
 [HideInInspector][SerializedField]
           private int a;
           public int b
           {
```