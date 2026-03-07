---
title: "Unity中的特殊文件夹及脚本编译顺序"
author: "mona"
date: 2020-09-16T21:30:00+08:00
categories: ["Unity"]
tags: []
---

## 特殊文件夹

- Standard Assets--存放Unity的标准资产。（只能放在Assets根目录下，有且仅有一个）
- Pro Standard Assets--存放Unity的专业级标准资产。（只能放在Assets根目录下，有且仅有一个）
- ActionScript--存放Flash的ActionScript脚本。当游戏被导出为Flash格式时，这里的脚本会自动替换指定的C#脚本。
- Plugins--存放插件。该文件夹有很多用途，须详细了解可以参考[官方文档](http://docs.unity3d.com/Manual/ScriptCompileOrderFolders.html)。（只能放在Assets根目录下，有且仅有一个）
  - Plugins/Android--存放Android插件，包括 .jar 或XML文件等。
  - Plugins/iOS--存放iOS插件，包括 .a 或 .m、.mm文件等。
- Editor--存放编辑器脚本。该文件夹里的所有资源文件或脚本文件都不会被打包进发布包中，并且脚本也只能在编辑器中使用。（可以放在Assets根目录下，也可以放在其它子目录里，数量不限）
- Editor Default Resources--存放编辑器用到的资源，如图片、文本文件等。它里面的资源不会被打包进发布包中，我们可以通过EditorGUIUtility.Load方法读取该文件夹下的资源。（只能放在Assets根目录下，有且仅有一个）
- Resources--存放需要动态读取的资源。该文件夹下的资源不管你用不用，它都会被打包进发布包中。在打包前可以用AssetDatabase.MoveAsset方法把不必要的资源从文件夹中移出去；打完包后再把移出去的资源移回来。（可以放在Assets根目录下，也可以放在其它子目录里，数量不限）
- Gizmos--通常是存放TIF格式的图片，在OnDrawGizmos方法内使用Gizmos.DrawIcon将其画为图标在场景中显示。（只能放在Assets根目录下，有且仅有一个）
- WebPlayerTemplates--存放网页模板，其中每个模板均以一个自定义名称的子目录形式存放。如果WebPlayerTemplates文件夹在Assets根目录下，那么它里面的所有脚本都不会被编译。否则，WebPlayerTemplates文件夹存在于其它子目录中，那它里面的所有脚本都会被编译。
-  StreamingAssets--存放媒体文件，如视频等。该文件夹下的资源会打包进发布包，它和Resources文件夹的区别是：Resources文件夹下的资源在打包时会被压缩，但StreamingAssets文件夹下的资源不会被压缩，而是原封不动的打包进发布包中。另外需要注意：此路径在PC/Mac电脑上可对文件实施“增删改查”等操作（在Windows8系统上进行增删等写入操作时报路径不允许访问），但在移动端只支持读取操作。（只能放在Assets根目录下，有且仅有一个）

## 脚本编译顺序

阶段 1：**Standard Assets**，**Pro Standard Assets** 和 **Plugins** 文件夹里的***运行时脚本***被编译。

阶段 2：**Standard Assets/Editor**，**Pro Standard Assets/Editor** 和 **Plugins/Editor** 文件夹里的***编辑器脚本***被编译。

阶段 3：所有不在**Editor**文件夹中的脚本被编译。

阶段 4：剩下的脚本（即在**Editor**文件夹中的脚本）被编译。