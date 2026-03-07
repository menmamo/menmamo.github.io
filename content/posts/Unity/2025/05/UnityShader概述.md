---
title: "UnityShader概述"
author: "mona"
date: 2025-05-27T21:38:00+08:00
categories: ["Unity"]
tags: ["shader", "unityshader入门精要笔记"]
---

## 材质和UnityShader

总体来说，在Unity中我们需要配合使用材质（Material）和Unity Shader才能达到需要的效果

常见的流程是

- 创建一个材质
- 创建一个Unity Shader，并把它赋给上一步中创建的材质
- 把材质赋给要渲染的对象
- 在材质面板中调整Unity Shader的属性

![](/usr/uploads/2025/05/2581152968.png)

Unity Shader定义了渲染所需的各种代码（如顶点着色器和片元着色器）、属性（使用那些纹理等）和指令（渲染和标签设置等）。

而材质允许我么调节这些属性，并将其最终赋给相应的模型

------

## Unity中的材质

Unity中的材质需要结合一个GameObject的Mesh 或者Particle Systems组件来工作，

他决定了我们的游戏对象看起来是什么样子的。

我们可以在Project视图中右键点击->Create->Material来创建

默认情况下，一个新建的啊辞职将使用Unity内置的Standard Shader。

对于美术人员来说，材质是他们非常熟悉的一种事物，Unity的材质和许多建模软件（Maya、C4D等）中提供的材质功能类似。

他们都提供了一个面板来调整各个参数。

![](/usr/uploads/2025/05/3322760765.png)

![](/usr/uploads/2025/05/3478383046.png)

------

## Unity中的Shader

一个单独的Unity Shader是无法发挥任何作用的，他必须和材质集合起来。

为此我们需要在材质面板的最上方选择我们想要使用的Unity Shader。

选择完毕后，材质面板会出现该Unity Shader可用的各种属性，颜色 纹理等。 

UnityShader 本质上就是一个文本文件，和Unity中的很多外部文件类似，Unity Shader也有导入设置面板。

在该面板上，我们可以在Default Maps中指定该Unity Shader使用的默认纹理。

当任何材质第一次使用该Unity Shader时，这些纹理里就会自动被赋到相应的属性上。

在下方面板中，Unity会显示出该Unity Shader相关的信息，例如他是否是一个表面着色器，是否是一个固定函数着色器。

还有一些信息是和我们在Unity Shader中的标签设置有关，例如是否会投射阴影，使用的渲染队列，LOD值等。

对于表面着色器，我们可以通过Show generated code按钮来打开一个新文件

该文件将显示Unity在背后为该表面着色器生成的 顶点、片元着色器。

如果该Unity Shader是一个固定函数着色器，在Fixed function的后面 也会出现一个Show generated  code按钮

来让我们检查该固定函数着色器生成的 顶点、片元着色器。

Compile and show code下拉列表 可以让开发者检查该Unity Shader针对不同图像编程接口，最终编译成的Shader代码

![](/usr/uploads/2025/05/2519088644.png)

![](/usr/uploads/2025/05/2519088644.png)

除此之外UnityShader的导入面板 还可以方便的检查其使用的渲染队列、是否关闭批处理、属性列表等信息。

------

来源作者：《UnityShader入门精要》冯乐乐