---
title: "ShaderLab-渲染标签Tags"
author: "mona"
date: 2025-06-16T20:15:00+08:00
categories: ["Unity"]
tags: ["shader"]
---

## SubShader中的标签

Pass渲染通道语句块中也可以声明

下叙标签仅可以在SubShader语句块中声明

------

### 渲染标签的语法结构

```
Tags{ "标签名1" = "标签值1" "标签名2" = "标签值2" "标签名2" = "标签值2" .......}
```

我们可以通过标签来确定什么时候以及如何对物体进行渲染

渲染标签是通过键值对的形式进行声明的

渲染标签没有数量限制

------

### 渲染队列Queue

渲染队列用来确定物体的渲染顺序

| 渲染队列    | 队列名称 | 队列号 | 说明                                                         | 例子                            |
| ----------- | -------- | ------ | ------------------------------------------------------------ | ------------------------------- |
| Background  | 背景     | 1000   | 最早被渲染的物体的队列，一般用来渲染天空盒或者背景           | Tags{ "Queue" = "Background" }  |
| Geometry    | 几何     | 2000   | 不透明的几何体通常使用该队列，当没有声明渲染队列时，Unity会默认使用这个队列 | Tags{ "Queue" = "Geometry" }    |
| AlphaTest   | 透明测试 | 2450   | 有透明通道的，需要进行Alpha测试的几何体会使用该队列，当所有Geometry队列实体绘制完后再绘制AlphaTest队列，效率更高 | Tags{ "Queue" = "AlphaTest" }   |
| Transparent | 透明     | 3000   | 该队列中几何体按照由远到近的顺序进行绘制，半透明物体的渲染队列，所有进行透明混合的几何体都应该使用该队列比如：玻璃材质，粒子特效等 | Tags{ "Queue" = "Transparent" } |
| Overlay     | 覆盖     | 4000   | 用是放在最后渲染的队列，于叠加渲染的效果，比如镜头光晕等     | Tags{ "Queue" = "Overlay" }     |

还有一种自定义队列

基于Unity预先定义好的这些渲染队列标签来进行加减运算来定义自己的渲染队列

比如：

```
Tags{ "Queue" = "Geometry+1" }    代表的队列号就是 2001
Tags{ "Queue" = "Transparent-1" } 代表的队列号就是 2999
```

自定义队列在一些特殊情况下，特别有用

比如 一些水的渲染 想要在不透明物体之后，半透明物体之前进行渲染，就可以自定义

注意：自定义队列只能基于预先定义好的各类型进行计算，不能在Shader中直接赋值数字

如果实在想要直接赋值数字，可以在材质面板中进行设置

------

### 渲染类型RenderType

对着色器进行分类，之后可以用于着色器替换功能

摄像机上有对应的API，可以指定这个渲染类型来替换成别的着色器

| 渲染类型          | 类型名   | 作用                                         | 例子                                       |
| ----------------- | -------- | -------------------------------------------- | ------------------------------------------ |
| Opaque            | 不透明的 | 用于普通Shader，比如：不透明、自发光、反射等 | Tags{ "RenderType" = "Opaque" }            |
| Transparent       | 透明的   | 用于半透明Shader，比如：透明、粒子           | Tags{ "RenderType" = "Transparent" }       |
| TransparentCutout | 透明切割 | 用于透明测试Shader，比如：植物叶子           | Tags{ "RenderType" = "TransparentCutout" } |
| Overlay           | 覆盖     | 用于GUI纹理、Halo（光环）、Flare（光晕）     | Tags{ "RenderType" = "Overlay"             |

还有一些不常用的渲染类型 用于Unity自带的地形系统

- TreeOpaque用于地形系统中的树干
- TreeTransparentCutout用于地形系统中的树叶
- TreeBillboard用于地形系统中的Billboarded树
- Grass用于地形系统中的草
- GrassBillboard用于地形系统中的Billboarded草

------

### 禁用批处理

当使用批处理时，模型会被变换到世界空间中，模型空间会被丢弃

这可能会导致某些使用模型空间顶点数据的Shader最终无法实现想要的结果

可以通过开启禁用批处理来解决该问题

**禁用批处理**

```
Tags{ "DisableBatching" = "True" }
```

**不禁用批处理（默认值）**

```
Tags{ "DisableBatching" = "False" }
```

**LOD效果激活时才会禁用批处理，主要用于地形系统上的树**

```
Tags{ "DisableBatching" = "LODFading" }
```

------

### 禁止阴影投影

控制该SubShader的物体是否会投射阴影

**不投射阴影**

```
Tags{ "ForceNoShadowCasting" = "True" }
```

**投射阴影（默认值）**

```
Tags{ "ForceNoShadowCasting" = "False" }
```

------

### 忽略投影机

物体是否受到Projector（投影机）的投射

**忽略Projector（一般半透明Shader需要开启该标签）**

```
Tags{ "IgnoreProjector" = "True" }
```

**不忽略Projector（默认值）**

```
Tags{ "IgnoreProjector" = "False" }
```

------

### 其他标签

#### 是否用于精灵

想要将该SubShader用于Sprite时，将该标签设置为False

```
Tags{ "CanUseSpriteAtlas" = "False" }
```

------

#### 预览类型

材质在预览窗口默认为球形，如果想要改变为平面或天空盒

只需要改变预览标签即可

**平面**

```
Tags{ "PreviewType" = "Panel" }
```

**天空盒**

```
Tags{ "PreviewType" = "SkyBox" }
```



------

## Pass中的渲染标签

Pass中的渲染标签语法和SubShader中相同

Pass当中有自己专门的渲染标签

------

### 光照模式LightMode

指定了该Pass应该在哪个阶段执行

可以将着色器代码分配给适当的渲染阶段，以实现所需的效果

| 光照模式      | 作用                                                         | 例子                              |
| ------------- | ------------------------------------------------------------ | --------------------------------- |
| Always        | 始终渲染；不应用光照                                         | Tags{"LightMode"="Always"}        |
| ForwardBase   | 在前向渲染中使用；应用环境光、主方向光、顶点/SH 光源和光照贴图 | Tags{"LightMode"="ForwardBase"}   |
| ForwardAdd    | 在前向渲染中使用；应用附加的每像素光源（每个光源有一个通道） | Tags{"LightMode"="ForwardAdd"}    |
| Deferred      | 在延迟渲染中使用；渲染 G 缓冲区                              | Tags{"LightMode"="Deferred"}      |
| ShadowCaster  | 将对象深度渲染到阴影贴图或深度纹理中                         | Tags{"LightMode"="ShadowCaster"}  |
| MotionVectors | 用于计算每对象运动矢量                                       | Tags{"LightMode"="MotionVectors"} |
| PrepassBase   | 在旧版延迟光照中使用；渲染法线和镜面反射指数                 | Tags{"LightMode"="PrepassBase"}   |
| PrepassFinal  | 在旧版延迟光照中使用；通过组合纹理、光照和反光来渲染最终颜色 | Tags{"LightMode"="PrepassFinal"}  |
| Vertex        | 当对象不进行光照贴图时在旧版顶点光照渲染中使用；应用所有顶点光源 | Tags{"LightMode"="Vertex"}        |
| VertexLMRGBM  | 当对象不进行光照贴图时在旧版顶点光照渲染中使用；在光照贴图为 RGBM 编码的平台上（PC 和游戏主机） | Tags{"LightMode"="VertexLMRGBM"}  |
| VertexLM      | 当对象不进行光照贴图时在旧版顶点光照渲染中使用；在光照贴图为双 LDR 编码的平台上（移动平台） | Tags{"LightMode"="VertexLM"}      |

Unity默认使用前向渲染，如果Unity使用前向渲染，而Pass使用延迟渲染（Deferred），那么这个Pass是不会进行渲染的

不声明LightMode，则默认使用 “Always” 这个模式，此时光照参数比如_LightColor0、_WorldSpaceLightPos0 没有进行正确的计算，直接使用可能会引发不可预见的错误

关于向前渲染、延迟渲染、旧版光照等概念

https://docs.unity.cn/cn/2019.4/Manual/RenderingPaths.html

------

### RequireOptions

用于指定当满足某些条件时才渲染该Pass

目前Unity仅支持

```
Tags{ "RequireOptions" = "SoftVegetation" }
```

仅当Quality窗口中开启了SoftVegetation时才渲染此通道

------

### PassFlags

一个渲染通道Pass可指示一些标志来更改渲染管线向Pass传递数据的方式

目前Unity仅支持

```
Tags{ "PassFlags" = "OnlyDirectional" }
```

在 ForwardBase 向前渲染的通道类型中使用时，此标志的作用是仅允许主方向光和环境光/光照探针数据传递到着色器
这意味着非重要光源的数据将不会传递到顶点光源或球谐函数着色器变量

