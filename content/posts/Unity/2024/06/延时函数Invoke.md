---
title: "延时函数Invoke"
author: "mona"
date: 2024-06-17T11:43:00+08:00
categories: ["Unity"]
tags: ["MonoBehavior"]
---

## 延时函数

- 会延时执行的函数
- 可以自己设定要执行的函数和延时的时间
- 是Mno基类中的方法
- 如果脚本依附的对象失活 延时函数依然可以执行
- 如果被移除 则无法执行

```c#
Invoke("函数名",5);//单位秒 不能传入参数
```

### 延时重复执行的函数

```c#
InvokeRepeating("函数名",5,2);//第一次延迟5秒执行 然后每隔2秒执行一次
```

### 取消延时函数的执行

```c#
CancelInvoke();//取消所有的
CancelInvoke("函数名");//取消指定的延时函数方法
```

### 判断是否有延时函数

```c#
IsInvoking();//只要有就返回true
IsInvoking("函数名");//是否有指定的延时函数
```