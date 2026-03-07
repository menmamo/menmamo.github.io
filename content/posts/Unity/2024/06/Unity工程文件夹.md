---
title: "Unity工程文件夹"
author: "mona"
date: 2024-06-17T11:48:00+08:00
categories: ["Unity"]
tags: []
---

## Unity工程文件夹

1. **Assets:**工程资源文件夹（美术资源、脚本等等）
2. **Library:**库文件夹（Unity自动生成管理）
3. **Logs:**日志文件夹、记录特殊信息（Unity自动生成管理）
4. **Packages:**包配置信息（Unity自动生成管理）
5. **ProjectSettings:**工程设置信息（Unity自动生成管理）

### 工程路径获取

- 一般只在编辑模式下使用
- 游戏发布后这个路径就不存在了

```c#
Application.dataPath;
```

### Resources 资源文件夹

- 默认没有这个文件夹 需要自己创建
- 需要通过Resources相关API**动态加载**的资源需要放在其中
- 该文件夹下所有文件都会被打包出去
- 打包时Unity会对其压缩加密
- 该文件夹打包后只读 只能通过Resources相关api加载
- 可以有多个Resources文件夹

### StreamingAssets流动资源文件夹

- 打包出去不会被压缩加密
- 移动平台只读 pc平台可读写
- 可以放入一些需要自定义动态加载的初始资源

#### 路径获取

```c#
Application.streamingAssetPath;
```

### persistentDataPath持久数据文件夹

- 不需要我们自己创建
- 每个平台的路径不同
- 所有平台可读写 一般用于放置动态下载或者动态创建的文件，游戏中创建或者获取的文件都放在其中

```c#
Application.persistentDataPath;
```

### plugins插件文件夹

- 一般不获取
- 需要我们自己创建
- 不同平台的插件相关文件放在其中 比如ios或者android平台

### Editos编辑器文件夹

- 需要我们自己创建
- 开发Unity编辑器时，编辑器相关脚本放在该文件夹中
- 该文件夹内容不会被打包出去

### Standard Assets默认资源文件夹 

- 默认资源文件夹
- 一般Unity自带资源都放在这个文件夹下
- 代码和资源优先被编译