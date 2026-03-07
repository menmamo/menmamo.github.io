---
title: "AssetBundle学习笔记"
author: "mona"
date: 2020-09-22T18:19:00+08:00
categories: ["Unity"]
tags: []
---

## AssetBundle（AB包）是什么？

AB包是特定于平台的资产压缩包（模型、贴图、预制件、音效、材质球等）

- AB包可以更好的管理资源相对于Resources 
- Resources下的资源是只读的 无法修改
- AB包的存储位置可以自定义
- 压缩方式自定义
- 可以对资源进行热更新
- C#脚本不能被打包

------

## 减少包体大小的方法

- 压缩资源
- 减少初始包大小（从服务器端获取资源）

------

## AssetBundle打包（AssetBundles-Browser插件）

### Build打包设置

**BuildTarget**
目标平台

**OutPut Path**
输出路径

**Clear Folders**
是否清空文件夹 重新打包

**Copy to StreamingAssets**
是否拷贝到StreamingAssets文件夹

- StreamingAssets–存放媒体文件，如视频等。该文件夹下的资源会打包进发布包，它和Resources文件夹的区别是：Resources文件夹下的资源在打包时会被压缩，但StreamingAssets文件夹下的资源不会被压缩，而是原封不动的打包进发布包中。另外需要注意：此路径在PC/Mac电脑上可对文件实施“增删改查”等操作（在Windows8系统上进行增删等写入操作时报路径不允许访问），但在移动端只支持读取操作。（只能放在Assets根目录下，有且仅有一个）

------

### AdvancedSettings 高级设置

**Compression（压缩方式）**

- No Compression（不压缩）
- Standard Compression（LZMA）
  - 解加载时间更快
- Chunk Based Compression（LZ4）
  - 内存占用更低
  - 解压缩时间更快

**Exclude Type Information**
在资源包中 是否包含资源的类型信息

**Force Rebuild**
重新打包时 是否重新构建包 和Clear Folders不同，它不会删除不再存在的包

**Ignore Type Tree Changes**
增量构建检查时 是否忽略类型数的更改

**Append Hash**
是否将文件的哈希值附加到资源包名上

**Strict Mode**
严格模式，如果打包时报错了，则打包直接失败

**Dry Run Build**
运行时构建

------

## AB包相关方法

**加载AB包**

```c#
//同步加载
ab = AssetBundle.LoadFromFile(path)；
//异步加载
abcr = AssetBundle.LoadFromFileAsync(path);
abcr.assetBundle
```

**获取AB包路径**

```c#
string path = Application.streamingAssetPath;
//AB包不能重复加载 否则会报错
```

**加载AB包中的资源**

```c#
//同步加载
ab.LoadAsset("");
ab.LoadAsset<GameObject>("");
ab.LoadAsset("",typeOf(GameObject)) as GameObject;
//Lua不支持泛形 所以用这种方式
//异步加载
abq = abcr.LoadAssetAsync(name,typeOf());
abq.asset as GameObject;
```

**卸载AB包**

```c#
ab.UnLoad();//卸载单个ab包
AssetBundle.UnloadAllAssetBundle() //卸载所有AB包
//参数：false只卸载AB包 
//true AB包加载的资源也一起销毁
```

------

## AB包依赖

如果AB包中的资源 引用了另一个ab包中的资源 如果只加载自己的AB包 则会出现资源丢失的情况

解决方法：

- 把依赖包一起加载
- 或者打包时将依赖一起打包