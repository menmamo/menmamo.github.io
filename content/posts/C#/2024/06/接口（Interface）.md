---
title: "接口（Interface）"
author: "mona"
date: 2024-06-17T11:36:00+08:00
categories: ["C#"]
tags: []
---

### 接口

- 接口是行为的抽象规范  使用关键字interface声明
- 接口只包含方法、属性、索引器、事件
- 成员不能被实现 并且不可以是私有的
- 接口只能继承接口
- 一个类可以继承多个接口
- 类继承接口后必须要实现接口中所有的成员
- 接口不可以被实例化，但可以作为容器存储对象 遵循里氏替换原则

```c#
//接口的命名规范I开头 帕斯卡命名
interface IFly{
    //方法
	void Fly();
    //属性 不可以写实现
    string Name{get;set;}
    //索引器
    int this[int index]{get;set;}
    //事件
    event Action doSomthing;
}

class Person:Ifly{
     //要实现接口中所有的成员
    public void Fly(){}
    public string Name{get;set;}
    public int this[int index]{get;set;}
    public event Action doSomthing; 
    
}
```

**实现的接口函数 可以加virtual 再在子类重写**

```c#
class Person:Ifly{
     //要实现接口中所有的成员
    public virtual void Fly(){
        onsole.WriteLine("I want fly!!!");
    }
    public string Name{get;set;}
    public int this[int index]{get;set;}
    public event Action doSomthing; 
    
}

class SuperMan:Person{
    public override void Fly(){
        Console.WriteLine("I can fly!!!");
    }   
}
```

**接口的显式实现**

- 当一个类继承两个接口
- 但是接口中存在同名方法时 可以使用显式实现
- 显示实现接口 不可以写访问修饰符
- 使用接口名.方法名 显式实现

```c#
class SuperMan:Person{
    public override void Person.Fly(){
        Console.WriteLine("I can fly!!!");
    }   
}

SuperMan p = new SuperMan();
//p.Fly() 报错
(p as Person).Fly();
```