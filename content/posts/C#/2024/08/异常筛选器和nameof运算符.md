---
title: "异常筛选器和nameof运算符"
author: "mona"
date: 2024-08-27T11:03:00+08:00
categories: ["C#"]
tags: []
---

## 异常筛选器用法

在异常捕获语句块中的Catch语句后通过加入when关键词来筛选异常

 when（表达式）该表达式返回值必须为bool值，如果为ture则执行异常处理，如果为false，则不执行

作用：用于筛选异常

好处：帮助我们更准确的排查异常，根据异常类型进行对应的处理

```c#
try
{
    //用于检查异常的语句块
}
catch (System.Exception e) when(e.Message.Contains("301"))
{
    //当错误编号为301时  作什么处理
    print(e.Message);
}
catch (System.Exception e) when (e.Message.Contains("404"))
{
    //当错误编号为404时  作什么处理
    print(e.Message);
}
catch (System.Exception e) when (e.Message.Contains("21"))
{
    //当错误编号为21时  作什么处理
    print(e.Message);
}
catch (System.Exception e)
{
    //当错误编号为其它时  作什么处理
    print(e.Message);
}
```

## nameof运算符

用法：nameof(变量、类型、成员)通过该表达式，可以将他们的名称转为字符串

作用：可以得到变量、类、函数等信息的具体字符串名称

```c#
int i = 10;
print(nameof(i));

print(nameof(List<int>));
print(nameof(List<int>.Add));

print(nameof(UnityEngine.AI));

List<int> list = new List<int>() { 1,2,3,4};
print(nameof(list));
print(nameof(list.Count));
print(nameof(list.Add));
```

