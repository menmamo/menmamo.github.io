---
title: "C# 虚函数"
author: "mona"
date: 2022-08-03T20:43:00+08:00
categories: ["Unity"]
tags: []
---

当子类继承父类，子类重写父类的方法时，如果使用父类装子类对象 调用重写的方法时 会执行父类中的方法。

可将父类的函数声明为虚函数，子类使用override关键字重写父类方法，调用重写的方法时 会执行子类中的方法。

```c#
using System;

namespace csharpleran
{
    struct StructTest
    {

        public class Fater {
            public Fater(int i) {
                Console.WriteLine(i);
            }

            public virtual void Eat(string str) {
                Console.WriteLine("Fater virtual Eat");
            }

            public void Eat() {
                Console.WriteLine("Fater Eat");
            }
        }

        public class Player : Fater {

            /// <summary>
            /// 子类调用父类的构造函数
            /// </summary>
            /// <param name="i"></param>
            public Player(int i):base(i)
            {
                Console.WriteLine("子类子类子类");
            }

            /// <summary>
            /// 调用重载构造函数
            /// </summary>
            public Player() : this(1) { 
                
            
            }

            public override void Eat(string str)
            {
                Console.WriteLine("Player override Eat");
            }

            public new void Eat()
            {
                Console.WriteLine("Player Eat");
            }
        }

        class Program
        {
         
            static void Main(string[] args)
            {
                Fater f = new Player();
                f.Eat(); //Fater Eat
                (f as Player).Eat();//Player Eat
                f.Eat("");//Player override Eat
                (f as Fater).Eat("");//Player virtual Eat

            }
        }
    }
}
```