---
title: "List"
author: "mona"
date: 2024-06-17T11:47:00+08:00
categories: ["C#"]
tags: ["C#语法"]
---

## List

- List是C#为我们封装好的类
- 它的本质是一个可变类型的泛型数组
- List类帮助我们实现了很多方法 

```c#
List<int> list = new List<int>();
List<int> list2 = new List<int>();
//添加数据
list.Add(1);
//添加另一个list进去
list.AddRange(list2);

//移除指定的元素
list.Remove(1);
//移除指定位置的元素
list.RemoveAt(0);
//清空
list.Clear();

//查
list[下标];
//查看元素是否存在
bool have1 = list.Contains(1);

//正向查找元素位置 找到了返回位置 找不到返回-1
int index = list.IndexOf(1);
//反向
inde = list.LastIndexOf(1);

//获取长度
list.Count;
//获取容器容量
list.Capacity;
```

