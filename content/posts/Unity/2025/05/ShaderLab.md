---
title: "ShaderLab"
author: "mona"
date: 2025-05-28T23:22:00+08:00
categories: ["Unity"]
tags: ["shader", "unityshader入门精要笔记"]
---

## 什么是ShaderLab

学习和编写着色器的过程 一直是一个学习曲线很陡峭的过程，通常情况下为了自定义渲染效果 往往需要和很多文件和设置打交道。而且一些细节问题往往也需要开发者花费很多时间去解决。

Unity为了解决上述问题，提供了一层抽象——Unity Shader，而我们和这层抽象打交道的途径 就是使用Unity提供的一种专门为Unity shader服务的语言——Shader Lab



![](/usr/uploads/2025/05/2626368639.png)

在Unity中所有的Unity Shader都是使用ShaderLab编写的。ShaderLab是Unity提供的编写Unity Shader的一种说明性语言。

它使用了一些嵌套在花括号内部的语义 来描述一个Unity shader文件的结构。

这些结构包含了许多渲染所需的数据

例如`Properties`语句块中定义了着色器所需的各种属性，这些属性会显示在材质面板上。

一个Unity Shader的结构如下所示

```
Shader "ShaderName"{
	Properties{
	//属性
	}
	SubShader{
	//显卡A使用的子着色器
	}
	SubShader{
	//显卡B使用的子着色器
	}
	Fallback "VertexLit"
}
```

Unity在背后会根据使用的平台 把这些结构编译成真正的代码和Shader文件，而开发者只需要和Unity shader打交道即可。

------

## Unity shader的结构

### 给Shader起个名字

每个Unity Shader文件的第一行都要通过`Shader`语义来指定该Shader的名字，例如"MyShader"

当为材质选择使用的Unity Shader时 这些名字就会出现在材质面板的下拉列表里

通过在字符串中添加斜杠  可以控制Unity Shader在材质面板中出现的位置 例如：

```
shader "Custom/MyShader"{}
```

------

### Properties

`Properties`语义块中 包含了一系列属性

这些属性将会出现在材质面板中

```
Properties{
	Name {"display name", PropertyType} = DefaultValue
	Name {"display name", PropertyType} = DefaultValue
	//更多属性
}
```

`Properties`是为了在材质面板中能够方便的调整材质的属性

如果我们需要在Shader中访问他们，就需要使用每个属性的名字（name）

在Unity中 属性的名字通常由一个下划线开始

显示名称（Display Name）则是出现在材质面板上的名字，

我们还需要为每个属性指定他的类型（PropertyType）

除此之外 还需要为每个属性指定一个默认值，在第一次把Shader赋给某个材质时 材质面板上就会默认显示这些值

 **Properties支持的属性类型**

| 属性类型       | 默认值的定义语法              | 例子                               |
| -------------- | ----------------------------- | ---------------------------------- |
| Int            | number                        | _Int("Int",Int)=2                  |
| Float          | number                        | _Float("Float",Float)=1.5          |
| Range(min,max) | number                        | _Range("Range",Range(0.0,5.0))=3.0 |
| Color          | (number,number,number,number) | _Color("Color",Color) = (1,1,1,1)  |
| Vector         | (number,number,number,number) | _Vector("Vector",Vector)=(2,3,1,3) |
| 2D             | "defaulttexture"{}            | _2D("2D",2D)=""{}                  |
| Cube           | "defaulttexture"{}            | _Cube("Cube",Cube)="white"{}       |
| 3D             | "defaulttexture"{}            | _3D("3D",3D)="black"{}             |

对于Int、Float、Range这些数字类型的属性 默认值就是一个单独的数字

对于Color和Vector这类似属性 默认值就是用圆括号包围一个四维向量

对于2D、Cube、3D这三种纹理属性，默认值稍复杂一些 他的默认值通过一个字符串后跟一个花括号

其中字符串要么是空的 要么是内置的纹理名称（white、black、gray、bump等）

花括号的用处原本是用来指定一些纹理属性的 但在Unity5.0之后的版本 被移除了

```
Shader "Custom/ShaderLabProperties"{
    Properties{
        _Int("Int",Int)=2
        _Float("Float",Float)=1.5
        _Range("Range",Range(0.0,5.0))=3.0
        _Color("Color",Color) = (1,1,1,1)
        _Vector("Vector",Vector)=(2,3,1,3)
        _2D("2D",2D)=""{}
        _Cube("Cube",Cube)="white"{}
        _3D("3D",3D)="black"{}
        //更多属性
    }
    Fallback "Diffuse"
}
```

有时我们想要在材质面板上显示更多类型 例如使用bool变量来控制Shader中使用那种计算

Unity还允许我们重载默认的材质编辑面板 以提供更多的自定义数据类型

------

为了在Shader中可以访问到这些属性，我们需要在Cg代码中定义和这些属性类型相匹配的变量

另外 即使我们不再`Properties`中定义这些属性，也可以直接在Cg代码中定义变量，此时我们可以通过脚本向Shader中传递这些属性

因此`Properties`的作用仅是为了让这些属性可以出现在材质面板中

-------

### SubShader

每一个Unity Shader文件可以包含多个`SubShader`语句块

当Unity需要加载这个shader时 Unity会扫描叫所有的`SubShader`语句块，然后选择第一个能够再目标平台上运行的`SubShader`

如果都不支持的话，Unity会使用Fallback指定的Shader

Unity提供这种语义的原因在于，不同的显卡具有不同的能力，

例如一些旧的显卡只能支持一定数目的操作指令，而一些更高级的显卡可以支持更多的指令数，那么我们希望再旧的显卡上使用计算复杂度较低的着色器，在高级的显卡上使用计算复杂度较高的着色器，一边提供更出色的画面。

```
SubShader{
 //可选的
 [Tags]
 //可选的
 [RenderSetup]
 Pass{
 
 }
 //Other Passes
}
```

`SubShader`中定义了一系列Pass以及可选的状态（ [RenderSetup]）和标签（ [Tags]）设置。

每个Pass定义了一次完整的渲染流程，但如果Pss的数目过多，往往会造成渲染性能的下降。

因此我们应尽量使用最小数目的Pass。

状态和标签同样可以在Pass中声明，

不同的是，SubShader中的一些标签是特定的。

也就是说，这些标签设置和Pass中使用的标签是不一样的。而对于状态设置来说 其使用的语法是相同的。

但如果我们在SubShader中进行了这些设置，那么将会用于所有的Pass

------

**SubShader设置渲染状态**

ShaderLab提供了一系列渲染状态的设置指令，这些指令可以设置显卡的各种状态，例如是否开启混合、深度测试等

**常见的渲染状态设置选项**

| 状态名称 | 设置指令                                                    | 解释                                 |
| -------- | ----------------------------------------------------------- | ------------------------------------ |
| Cull     | Cull Back、Front、Off                                       | 设置剔除模式；剔除背面/正面/关闭剔除 |
| ZTest    | ZTest Less Greater、LEqual、GEqual、Equal、NotEqual、Always | 设置深度测试时使用的函数             |
| ZWrite   | Zwrite On、Off                                              | 开启/关闭深度写入                    |
| Blend    | Blend SrcFactor DstFactor                                   | 开启并设置混合模式                   |

当在SubShader块中设置上述渲染状态时，会应用到所有的Pass

如果不想这样，可以在Pass中进行设置

------

**SubShader设置标签**

Subshader的标签(Tags)是一个键值对，他的键和值都是字符串类型。

这些键值对是SubShader和渲染引擎之间的沟通桥梁，

他们用于告诉引擎 我希望怎样以及何时渲染这个对象

标签的结构如下

```
Tags{"TagName1"="Value1" "TagName2"="Value2"}
```

**SubShader支持的标签类型**

| 标签类型           | 说明                                                         | 例子                              |
| ------------------ | ------------------------------------------------------------ | --------------------------------- |
| Queue              | 控制渲染顺序，指定该物体属于哪一个渲染队列，通过这种方式可以保证所有的透明物体可以在不透明物体后面被渲染，我们也可以自定义使用的渲染队列来控制物体的渲染顺序 | Tags{"Queue"="Transparent"}       |
| RenterType         | 对着色器进行分类，例如这是一个不透明的着色器，或是一个透明的着色器等。这可以被用于着色器替换（Shader Replacement）功能 | Tags{"RenderType"="Opaque"}       |
| DisableBatching    | 一些SubShader在使用Unity批处理功能时会出现一些问题，这时可以通过该标签来直接指明是否对该SubShader使用批处理。 | Tags{"DisableBatching"="True"}    |
| ForceNoShowCasting | 控制使用该SubShader的物体是否会投射阴影                      | Tags{"ForceNoShadowCasting"=True} |
| IgnoreProjector    | 如果该标签值为True 那么使用该SubShader的物体不会受Projector的影响，通常用于半透明物体 | Tags{"IgnoreProjector"="True"}    |
| CanUseSpriteAtlas  | 当该SubShader用于精灵（Sprites）时,将该标签设置为"False"     | Tags{”CanUseSpriteAtlas“=”False“} |
| PreviewType        | 指明材质面板将如何预览该材质，默认情况下，材质将显示为一个球形态，我们可以通过把该标签设置为"Plane""SkyBox"来改变预览类型 | Tags{”PreviewType“=”Plane“}       |

需要注意的是上述标签仅可以在SubShader中声明 不可以在Pass中声明。

Pass块也可以定义标签，但这些标签是不同于SubShader的标签类型

------

### Pass

Pass语义块包含的语义如下

```
Pass{
    [Name]
    [Tags]
    [RenderSetup]
    //Other Code
}
```

首先我们可以在Pss中定义该Pass的名称

```
Name "MyPass"
```

通过这个名称，我们可以使用ShaderLab的UsePass命令来直接使用其他Unity Shader中的Pass 例如

```
UsePass "MyShader/MYPASS"
```

这可以提高代码复用性

需要注意的是 Unity内部会把所有Pass的名称转成大写字母

因此，在使用UsePass命令时必须使用大写字母的名字

------

**Pass设置渲染状态**

我们可以对Pss设置渲染状态，SubShader的状态设置 同样适用于Pass

除了可以设置渲染状态以外，我们还可以使用固定管线的着色器

------

**Pass设置标签**

Pass同样可以设置标签，但他的标签不同于SubShader的标签，这些标签也是用于告诉渲染引擎我们希望如何渲染物体

**Pass的标签类型**

| 标签类型       | 说明                                                         | 例子                                    |
| -------------- | ------------------------------------------------------------ | --------------------------------------- |
| LightMode      | 定义该Pass在Untiy的渲染流水线中的角色                        | Tags{"LightMode"="ForwordBase"}         |
| RequireOptions | 用于指定当满足某些条件时才渲染该Pass,他的值是一个由空格分隔的字符串，目前，Unity支持的选项有：SoftVegetation。在后面的版本中可能会增加更多选项。 | Tags{"RequireOptions"="SoftVegetation"} |

------

**特殊的Pass**

除了上面普通的Pass定义外，Unity Shader还支持一些特殊的Pass,以便进行代码复用或实现更复杂的效果

- UsePass 可以使用该命令复用其他Untiy Shader中的PAss
- GrabPass 该Pass负责抓取屏幕 并将结果存储在一张纹理中，一边用于后续的Pass处理

------

### FallBack

他用于告诉Unity，如果上面的SubShader在这快显卡上都不能运行，那么就使用这个最低级的Shader把

```
Fallback "ShaderName"
//或者
Fallback off
```

如上所属 我们通过一个字符串告诉Unity这个”最低级的Unity shader“是谁

我们也可以选择关闭Fallback功能，如果显卡跑不了所有的SubShader 那就不管他了

事实上Fallback还会影响 阴影的投射 在渲染阴影纹理时，Unity会在每个Unity shader中寻找一个阴影投射的Pass

通常情况下 我们不需要自己专门实现一个Pass

因为Fallback使用的内置shader中包含了这样一个通用的Pass，

因此 为每个Unity shader正确设置Fallback是非常重要的

```
Fallback "VertexLit"
```

------

### 其他语义

除了上述的语义，还有一些不常用的。

例如，如果我们不满足于Unity内置的属性类型，想要自定义材质面板的编辑界面，就可以使用`CustomEditor`语义来扩展编辑界面。

还可以使用`Category`语义来对Unity Shader中的命令进行分组等。

------

## Unity Shader的形式

尽管Unity Shader可以做的事情非常多，但其最重要的任务还是指定各种着色器所需的代码。

这些着色器代码可以写在SubShader中（表面着色器的做法），也可以写在Pass语句块中（顶点、片元着色器的做法）

在Unity中 我们可以使用下面3中形式来编写Shader

```
Shader "MyShader"{
    Properties{
        //所需的各种属性
    }
    SubShader{
        //真正意义上的Shader代码会出现在这里
        //表面、顶点、片元、固定函数着色器
    }
}
```

------

### 表面着色器

表面着色器是Unity自己创造的一种着色器代码类型

他需要的代码量很少，Unity在背后做了很多工作，但渲染代价比较大。

他本质上和顶点、片元着色器是一样的

也就是说 当编写一个表面着色器的时候，他背后仍旧把他转换成 顶点、片元着色器

我们可以理解为 表面着色器是对顶点、片元着色器更高一层的抽象

他的价值在于，Unity为我们从护理了很多光照细节，使得我们不再需要操心这些

```
Shader "Custom/Simple Surface Shader"{
    SubShader{
        Tags{"RenderType"="Opaque"}
        CGPROGRAM
        # pragma surface surf Lambert
        struct Input{
            float4 color : COLOR;
        };
        void surf (Input In, inout SurfaceOutPut o){
            o.Albedo = 1;
        }
        ENDCG
    }
    Fallback "Diffuse"
}
```

从上述程序中可以看出

表面着色器被定义在SubShader语义块（而非Pass语义块）中`CGPROGRAM`和`ENDCG`之间

原因是 表面着色器 不需要开发者关心使用多少个Pass、每个Pass如何渲染等问题

Unity会在背后为我们做好这些事情。

我们只需要告诉他“嘿，使用这些纹理去填充颜色，使用这个法线纹理去填充法线，使用Lambert光照模型”

`CGPROGRAM`和`ENDCG`之间的代码是使用Cg/HLSL 编写的

也就是说 我们需要把Cg/HLSL语言嵌套在ShaderLab语言中

值得注意的是 这里的Cg/HLSL 是经过Unity封装后提供的

他的语法和标准的Cg/HLSL几乎一样，但还是有细微不同，

例如有些原生的函数和用法 Unity 没有提供支持

------

### 顶点/片元着色器

在Unity中我们可以使用Cg/HLSL语言来编写 顶点/片元着色器

他们更加复杂，但灵活度更高。

一个简单的示例如下

```
Shader "Custom/Simple Surface Shader"{
    SubShader{
        Pass{
            CGPROGRAM
            
            #pragma vertex vert
            #pragma fragment frag
            
            float4 vert(float4 v : POSITION) : SV_POSITION{
                return mul(UNITY_MATRIX_MVP, v);
            }
            
            fixed4 frag() : SV_Target{
                return fixed(1.0,0.0,0.0,1.0);
            }
            ENDCG
        }
    }
}
```

和表面着色器类似，顶点、片元着色器的代码也需要定义在`CGPROGRAM`和`ENDCG`之间，

但不同的是，顶点、片元着色器是写在Pass语义块内，而非SubShader内的。

原因是我们需要自己定义每个Pass需要使用的Shader代码。

虽然我们可能需要编写更多的代码，但带来的好处是 灵活性更高。

更重要的是，我们可以控制渲染的实现细节，

------

### 固定函数着色器

上面两种Shader形式都使用了可编程渲染管线，而对于一些比较旧的设备（其GPU仅支持DirectX7.0、OpenGL1.5或者OpenGL ES 1.1），他们不支持可编程管线着色器。因此我们就需要使用固定函数着色器，这些着色器往往值可以完成一些非常简单的效果

```
Shader "MyShader"{
    Properties{
        _Color("Main Color",Color) = (1,1,1,1)
    }
    SubShader{
        Pass{
            Material{
                Diffuse[_Color]
            }
            Lighting On
        }
    }
}
```

固定函数定义在Pass语义块中，这些代码相当于Pass中的一些渲染设置

对于固定函数着色器来说，我们需要完全使用ShaderLab的语法（既使用ShaderLab的渲染命令来编写）

由于现在绝大多数的GPU都支持可编程的渲染管线，这种固定管线的编程方式已经被抛弃

在Unity5.2中 所有的固定函数着色器 都会被编译成对应的 顶点/片元着色器

因此真正意义上的固定函数着色器已经不存在了

------

来源作者：《UnityShader入门精要》冯乐乐