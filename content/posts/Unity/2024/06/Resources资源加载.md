---
title: "Resources资源加载"
author: "mona"
date: 2024-06-17T11:47:00+08:00
categories: ["Unity"]
tags: ["资源系统"]
---

## Resources资源加载

- 通过代码动态加载Resources文件夹下指定路径资源
- 在一个工程中 Resources文件夹可以有多个 打包时所有Resources文件夹里的资源都会被整合在一起
- 避免繁琐的拖拽操作

### 常用的资源类型

- 预设体对象 GameObject
  - 预设图对象加载需要实例化 其他资源加载一般可以直接用
- 音效文件 AudioClip
- 文本文件 TestAsset
- 图片文件 Texture
- 其他类型 需要什么就用什么类型

### 资源加载 普通方法

```c#
Object obj = Resources.Load("Cube");
Resources.load<GameObject>("Cube");//泛型
```

### Resources异步加载资源

- 异步加载不能马上得到加载的资源 至少要等一帧
- 异步加载就是内部新开一个线程进行资源加载 不会造成主线程卡顿

```c#
ResourceRequest rq = Resources.LoadAsync<GameObject>("路径");
//在加载完成后 会调用completed事件 参数AsyncOperation
rq.completed += LoadOver;
private void LoadOver( AsyncOperation rq)
{
    print("加载结束");
    //asset 是资源对象 加载完毕过后 就能够得到它
    obj = (rq as ResourceRequest).asset as GameObject;
}
```

### 通过携程加载资源

```c#
StartCoroutine(Load());
IEnumerator Load()
{
    ResourceRequest rq = Resources.LoadAsync<GameObject>("路径");
	//携程返回值yield return asyncOperation;等异步操作结束后再执行后续代码
    //ResourceRequest继承自asyncOperation
    //yield return rq;判断资源是否加载结束
    while(!rq.isDone)
    {
        //打印当前的 加载进度 
        //该进度 不会特别准确 过渡也不是特别明显
        print(rq.progress);
        yield return null;
    }
    obj = rq.asset as GameObject;
}
```

### 卸载资源

- 该方法不能释放GameObject对象 因为他会用于实例化对象
- 只能用于一些 不需要实例化的内容 图片 音效 文本等等
- 一般情况下很少单独使用它

```c#
var tex = Resources.Load<Testure>("Tex/TestJPG");
Resources.UnloadAsset(tex);
```

### 卸载未使用的资源

```c#
//一般在过场景时和GC一起使用
Resources.UnloadUnusedAssets();
GC.Clollect();
```

### 异步加载场景

```c#
AsyncOperation ao = SceneManager.LoadSceneAsync("场景名");
ao.completed +=(a)=>{
    Debug.log("场景加载完了");
}
```

### 通过携程异步加载场景

在加载场景时 unity会删除当前场景下所有的对象，携程脚本依附的物体如果被删除 那么携程就不会执行了所以需要特殊处理

```c#
IEnumerator LoadScene(string name)
{
    //第一步
    //异步加载场景
    AsyncOperation ao = SceneManager.LoadSceneAsync(name);
    //Unity内部的 协程协调器 发现是异步加载类型的返回对象 那么就会等待
    //等待异步加载结束后 才会继续执行 迭代器函数中后面的步骤
    print("异步加载过程中 打印的信息");
    //协程的好处 是异步加载场景时 我可以在加载的同时 做一些别的逻辑
    while(!ao.isDone)
    {
        print(ao.progress);
        yield return null;
    }
}
```