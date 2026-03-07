---
title: "ref和out关键字"
author: "mona"
date: 2024-06-17T11:47:00+08:00
categories: ["C#"]
tags: ["C#语法"]
---

### ref和out

- ref和out可以在函数内部 修改外部传入的内容（当传入的值类型参数在函数内部修改时 或引用类型的参数在内部重新声明时 外部的值会发生变化）
- ref和out是函数参数的修饰符
- ref可作为参数传入并修改（需要在外部赋值后传入），out只可以修改外部的值 不可以作为参数传入 必须在函数内部赋值
- ref传入的变量必须初始化 但是在内部 可改可不改
- out传入的变量不用初始化，但是在内部 必须赋值

```c#
public void ChangeString(ref string value){
	value = "测试啦啦啦";
}
string test = "99";
ChangeString(ref test);
Print(test);//输出测试啦啦啦
```