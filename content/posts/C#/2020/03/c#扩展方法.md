---
title: "c#扩展方法"
author: "mona"
date: 2020-03-07T20:17:00+08:00
categories: ["C#"]
tags: []
---

扩展方法可以用新方法扩展现有类型改变原始类型的定义。

扩展方法是静态类的静态方法，其中this修饰符应用于第一个参数。

第一个参数的类型将是扩展的类型。

```c#
public static class StringExtension {
//需要静态的class
   public static bool IsCapitalized (this string s) {
      if (string.IsNullOrEmpty(s))
         return false;
      return char.IsUpper (s[0]);
   }
}
```

IsCapitalized扩展方法可以被调用，就像它是一个字符串上的实例方法，如下所示：

```c#
Console.WriteLine ("abc".IsCapitalized());
```

一个扩展方法调用被转换回一个普通的静态方法调用：

```c#
Console.WriteLine (StringExtension.IsCapitalized ("abc"));
```

