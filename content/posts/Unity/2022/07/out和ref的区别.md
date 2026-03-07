---
title: "out和ref的区别"
author: "mona"
date: 2022-07-27T19:44:00+08:00
categories: ["Unity"]
tags: []
---

### 同点

可以在方法内部 影响外部传入的参数 让其可以被修改

### 区别

ref在函数调用前 初始化参数的变量

out在函数内部 初始化参数的变量

```c#
public void ChangeArray(ref int[] arr){
	arr[0] = 5;
}

public void ChangeArray(out int[] arr){
	arr = new int[]{5,4,3}
}
```