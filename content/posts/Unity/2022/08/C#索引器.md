---
title: "C#索引器"
author: "mona"
date: 2022-08-01T19:55:00+08:00
categories: ["Unity"]
tags: []
---

```c#
 class Program
        {
            private int[] test;
            public int this[int a] {
                get
                {
                    return test[a];
                }
                set
                {
                    if (test == null) {
                        test = new int[10];
                    }
                    test[a] = value;
                }                     
            }

            static void Main(string[] args)
            {
                Program p = new Program();
                p[5] = 10;
               
            }
        }
```

