---
title: "静态类（static class）"
author: "mona"
date: 2024-06-17T11:37:00+08:00
categories: ["C#"]
tags: []
---

### 静态类

- 用static修饰的类
- 只能包含静态成员
- 不能实例化（唯一性）

```c#
static class TestStatic{
	public static int index;
}
```

**静态构造函数**

- 在第一次使用时被自动调用
- 不可以使用访问修饰符
- 不能有参数

**静态类中的构造函数**

- 只会自动调用一次（不可以被实例化）
- 静态类的构造函数也是静态的

```C#
static class TestStatic{
	public static int index;
    static TestStatic(){
        index = 5;
    }
}
```

**普通类中的静态构造函数**

```c#
static class TestStatic{
    public static int index;
    static TestStatic(){
        index = 5;
    }
    //和静态的构造函数不同 不属于重载
    public TestStatic(){
        index = 4;
    }
}
```