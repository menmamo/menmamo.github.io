---
title: "C#中 (int) 、Int.Parse() 、Convert.toInt32()区别 "
author: "mona"
date: 2019-11-21T00:12:00+08:00
categories: ["C#"]
tags: []
---

**强制类型转换**

不遵循四舍五入，只截取整数部分；

```c#
(int)5.21       //输出5
```

------

**Int.Parse**

只支持将string类型转成int

```c#
String st="5.21";
double n=5.21;
Int.Parse(st);      //输出5
Int.Parse(n);     //报错
```

------

**Convert.ToInt32(double value)** 

不完全遵循四舍五入

如果 value 为两个整数中间的数字，则返回二者中的偶数

```c#
Console.WriteLine(Convert.ToInt32(4.3));  //四舍五入，输出4
Console.WriteLine(Convert.ToInt32(4.5));  //第一位小数为5时，4.5在4和5之间，输出偶数4
Console.WriteLine(Convert.ToInt32(4.53)); //四舍五入，输出5
Console.WriteLine(Convert.ToInt32(5.3));  //四舍五入，输出5
Console.WriteLine(Convert.ToInt32(5.5));  //第一位小数为5时，5.5在5和6之间，输出偶数6
Console.WriteLine(Convert.ToInt32(5.53)); //四舍五入，输出6
```

注意：

`Convert.ToInt32()`和`int.Parse()`对于空值的处理不同

- `Convert.ToInt32(null)`返回0 不抛出异常
- `int.Parse(null) `抛出异常

