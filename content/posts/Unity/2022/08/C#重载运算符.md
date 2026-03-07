---
title: "C#重载运算符"
author: "mona"
date: 2022-08-01T20:44:00+08:00
categories: ["Unity"]
tags: []
---

```c#
 class Program
        {
            public int x;
            public int y;

            public static Program operator + (Program p1 , Program p2) {
                return new Program()
                {
                    x = p1.x + p2.x,
                    y = p1.y + p2.y
                };
            }
          

            static void Main(string[] args)
            {
                Program p1 = new Program() { x = 1, y = 3};
                Program p2 = new Program() { x = 4 , y = 2};
                Program p3 = p1 + p2;
                Console.WriteLine(p3.x);                            
            }
        }
    }
```