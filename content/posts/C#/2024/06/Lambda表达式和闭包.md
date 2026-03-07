---
title: "Lambda表达式和闭包"
author: "mona"
date: 2024-06-17T11:46:00+08:00
categories: ["C#"]
tags: ["C#语法"]
---

###  ambda表达式

- 使用起来和匿名函数相同

```C#
Action a = ()=>{
	Console.WriteLine("无参无返回值");
};
Action<int> a1 = (int value )=>{Console.WriteLine("有参无返回值"+value);};
// 可以省略类型 如果只有一个参数 可以省略括号
a1 = value =>{Console.WriteLine("有参无返回值"+value);};
//有返回值 有参数
Func<int,string> f1= value => {return "1"+value;}; 
```

### 闭包

- 内层的函数可以用用包含在外层函数的变量
- 即使外层函数的执行已经终止
- 注意：
  - 该变量提供的值并非变量创建时的值，而是在父函数范围内的最终值

```c#
class Test{
    public Action action;
    public Test(){
        int value = 10;//value是临时的 一般情况下函数执行完就会被回收
        action = ()=>{
            //引用了临时变量value
            //此时value的生命周期被改变 value只有action=null时才会被释放
            Console.WriteLine(value);
        }
        
        for(int i = 0; i < 10; i++){//i临时变量
            action += ()=>{
                Console.WriteLine(i); //调用Action()打印999999999  i的最终值
            } 
        }
    }    
}
```