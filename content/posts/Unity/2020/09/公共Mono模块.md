---
title: "公共Mono模块"
author: "mona"
date: 2020-09-10T19:03:00+08:00
categories: ["Unity"]
tags: ["小框架", "MonoBehavior"]
---

## 作用：

1. 让没有继承Mono的模块 也可以被Unity生命周期函数调用或使用协程
2. 可以节约部分反射造成的性能开销

```c#
using ProjectBase.SingleBase;
using UnityEngine.Events;


public class MonoManager : SingleMono<MonoManager>
{
    private event UnityAction updateEvent;
    private event UnityAction fixedUpdateEvent;
    private void Update()
    {
        updateEvent?.Invoke();
    }

    private void FixedUpdate()
    {
        fixedUpdateEvent?.Invoke();
    }
    
    public void AddUpdateListener(UnityAction fun)
    {
        updateEvent += fun;
    }

    public void AddFixedUpdateListener(UnityAction fun)
    {
        fixedUpdateEvent += fun;
    }

    public void RemoveFixedUpdateListener(UnityAction fun)
    {
        fixedUpdateEvent -= fun;
    }

    public void RemoveUpdateListener(UnityAction fun)
    {
        updateEvent -= fun;
    }
    
}
```