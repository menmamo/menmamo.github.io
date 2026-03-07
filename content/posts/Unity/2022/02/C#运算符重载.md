---
title: "C#运算符重载"
author: "mona"
date: 2022-02-25T09:05:00+08:00
categories: ["Unity"]
tags: []
---

```c#
namespace 重载运算符
{
    class Box
    {
        public int length { get; set;}
        public int breadth { get; set;}
        public int height { get; set; }

        /// <summary>
        /// 运算符的重载
        /// </summary>
        /// <param name="box1"></param>
        /// <param name="box2"></param>
        /// <returns></returns>
        public static Box operator + (Box box1,Box box2) {
            Box box = new Box();
            box.length = box1.length + box2.length;
            box.breadth = box1.breadth + box2.breadth;
            box.height = box1.height + box2.height;
            return box;
        }

        public override string ToString()
        {
            return String.Format("长：{0},宽：{1}，高：{2}",length,breadth,height);
        }
    }

  class Program
    {
        static void Main(string[] args)
        {
            Box box1 = new Box();
            box1.height = 5;
            box1.length = 4;
            box1.breadth = 3;
            Box box2 = new Box();
            box2.height = 2;
            box2.length = 3;
            box2.breadth = 2;
            Console.WriteLine(box1+box2);
            Console.ReadKey();
        }
    }
}
```

### 输出结果

```shell
长：7，宽：5，高：7
```

### 可重载和不可重载运算符

| **运算符**                            | **描述**                                     |
| ------------------------------------- | -------------------------------------------- |
| +, -, !, ~, ++, --                    | 这些一元运算符只有一个操作数，且可以被重载。 |
| +, -, *, /, %                         | 这些二元运算符带有两个操作数，且可以被重载。 |
| ==, !=, <, >, <=, >=                  | 这些比较运算符可以被重载。                   |
| &&, &#124;&#124;                      | 这些条件逻辑运算符不能被直接重载。           |
| +=, -=, *=, /=, %=                    | 这些赋值运算符不能被重载。                   |
| =, ., ?:, ->, new, is, sizeof, typeof | 这些运算符不能被重载。                       |

