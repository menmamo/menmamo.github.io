---
title: "字符串string"
author: "mona"
date: 2024-06-17T11:45:00+08:00
categories: ["C#"]
tags: ["C#语法"]
---

## String

- 字符串的本质是一个Char数组
- 可使用String类内部的索引器 直接使用[下标]来得到对应的字符
- 也可通过.Length获取长度

```c#
string str = "test";
//string类 索引器
Console.WriteLine(str[0]);//t char类型
str.Length//获取字符串长度
```

**字符串转char数组**

```c#
char[] chars = str.ToCharArray(); //字符串转字符数组
string str3 = new string(chars)//字符数组转字符串
```

**字符串拼接**

```c#
string.Format("{0}{1}",11,33); //1133
```

**正向查找字符位置**

```c#
str = "我爱吃肉";
int index = str.IndexOf("吃肉");//2
index = str.IndexOf("吃菜");//-1
```

**反向查找**

```c#
str = "我爱吃肉我爱吃肉我爱吃肉";
int index = str.LastIndexOf("吃肉");//10
index = str.LastIndexOf("吃菜");//-1
```

**移除指定位置后的字符**

```c#
str = "我爱吃肉我爱吃肉我爱吃肉";
str = str.Remove(4);//我爱吃肉 不会改变原字符串 需要重新赋值一个新的
str = "我爱吃肉我爱吃肉我爱吃肉";
str = str.Remove(4,4);//参数1 开始索引  参数2 移除字符个数 结果：我爱吃肉我爱吃肉
```

**替换字符串**

```c#
str = "我爱吃肉我爱吃肉我爱吃肉";
str = str.Replace("吃肉","吃菜");
```

**大小写转换**

```c#
str = "aBcaBc";
str = str.ToUpper();//ABCABC
str = str.ToLower();//abcabc
```

**字符串截取**

```c#
str = "我爱吃肉你爱吃肉吗"
string str2 = str.Substring(3);//你爱吃肉吗
str2 = str.Substring(3,1);//你
```

**字符串切割**

```c#
str = "1,2,3,4,5,6";
string[] strs = str.split(','); 1  2  3  4  5  6
```

## StringBuilder

### StringBuilder介绍

- C#提供一个用于处理字符串的公共类
- 可以修改字符串但不创建新的对象，需要频繁修改和拼接的字符串可以使用它，可以提升性能
- 使用StringBuilder需要引用命名空间System.Text
- 扩容也会产生垃圾 只是没有string那么频繁而已

### StringBuilder的使用

```C#
//声明
StringBuilder str = new StringBuilder("我爱吃肉");
//重载 第二个参数 可以设置容量
StringBuilder str2 = new StringBuilder("我爱吃肉",100);
Console.WriteLine(str);//我爱吃肉
```

**StringBuilder存在一个容量的概念，容量满了会自动将容量X2（扩容）**

```c#
str.Capacity//默认容量16 当超过16个字符时 会将容量X2
str.Length//4
```

**增删改查 替换**

```c#
//增加 不可以使用算数运算符+
//在字符串的尾部增加
str.Append("你爱吃肉吗？");//我爱吃肉你爱吃肉吗
str.AppendFormat("{0}{1}{2}",100,99);//我爱吃肉你爱吃肉吗10099

//插入 第一个参数索引
str.Insert(1,"不");//我不爱吃肉你爱吃肉吗10099
//删
//第一个参数 索引，第二个参数删除多少个字符
str.Remove(5,10);//我不爱吃肉
str.Clear();//清空

//查
str = new StringBuilder("我爱吃肉");
str[1];//爱
//修改
str[1] = '想';//我想吃肉  string不可以这样修改 StringBuilder可以
//替换
str.Replace("想","爱");//我爱吃肉

//重新赋值 不产生垃圾
str.Clear();
str.Append("我爱吃肉");

//判断相等
str.Equals("我爱吃菜");//flase
```