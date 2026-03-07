---
title: "C#字符串比较"
author: "mona"
date: 2022-01-09T02:16:00+08:00
categories: ["Unity"]
tags: []
---

## CompareTo 这是字符串比较的函数，用法：

```c#
string s1 = “c”;
string s2 = “b”;
if(s1.CompareTo(s2)==1)
{
}
```

它有三个返回值~：

- 当s1>s2时，s1.CompareTo(s2)==1
- 当s1=s2时，s1.CompareTo(s2)0
- 当s1<s2时，s1.CompareTo(s2)-1

以上为例，c的asc大于b的asc，所以返回1