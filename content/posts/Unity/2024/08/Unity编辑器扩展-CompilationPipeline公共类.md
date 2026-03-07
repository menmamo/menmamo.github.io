---
title: "Unity编辑器扩展-CompilationPipeline公共类"
author: "mona"
date: 2024-08-08T21:50:00+08:00
categories: ["Unity"]
tags: ["编辑器扩展"]
---

## CompilationPipeline公共类是用来做什么的

它是 Unity 编辑器中的一个公共类，用于处理代码编译相关的操作和事件的

对于我们来说，我们最常用的是利用它得知代码是否编译结束

比如动态生成脚本时，我们需要在编译结束后才能使用新的脚本 

## 常用API

### 当一个程序集编译结束会主动调用的回调函数

传入的两个参数分别是：
        1. string arg1 : 编译完成的程序集名
        1. CompilerMessage[] arg2 : 编译完成后产生的编译消息数组，包括编译警告和错误信息

```c#
private void OnEnable()
{
    CompilationPipeline.assemblyCompilationFinishedCompilationPipeline_assemblyCompilationFinished;      
}
private void CompilationPipeline_assemblyCompilationFinished(string arg1, CompilerMessage[] arg2)
{
Debug.Log("程序集名：" + arg1);
}
```

### 当所有程序集编译结束会主动调用的回调函数

参数：

object obj: ActiveBuildStatus 活动生成状态对象

```c#
private void OnEnable()
{
    CompilationPipeline.compilationFinished += CompilationPipeline_compilationFinished;
}
private void CompilationPipeline_compilationFinished(object obj)
{
    Debug.Log("所有程序集编译结束"); 
}
```

