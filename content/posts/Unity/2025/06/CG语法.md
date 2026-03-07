---
title: "CG语法"
author: "mona"
date: 2025-06-19T14:24:00+08:00
categories: ["Unity"]
tags: ["shader"]
---

## CG语句写在哪里？

对于顶点\片元着色器来说

CG语句需要写在Pass渲染通道语句块中

我们需要在Pass语句块中加入指令`CGPROGRAM`

## 指定着色器函数

我们需要先使用 #pragma 声明编译指令

用来指定 顶点、片元着色器使用的函数

```
#pragma vertex name(实现顶点着色器的函数名)
#pragma fragment name(实现片元着色器的函数名)
```

之后我们只需要在这两个函数中书写Shader逻辑即可

------

## CG基础数据类型

| 类型          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| `uint`        | 32位无符号整型                                               |
| `int`         | 32位整型                                                     |
| `float`       | 32位浮点数 符号：f                                           |
| `half`        | 16位浮点数 符号：h                                           |
| `fixed`       | 12位浮点数                                                   |
| `bool`        | 布尔                                                         |
| `string`      | 字符串                                                       |
| `smapler`     | 纹理对象句柄（处理纹理（Texture）数据的数据类型）            |
| `sampler1D`   | 用于一维纹理，通常用于对一维纹理进行采样，例如从左到右的渐变色 |
| `sampler2D`   | 用于二维纹理，最常见的纹理类型之一。它用于处理二维图像纹理，例如贴图 |
| `sampler3D`   | 用于三维纹理，通常用于体积纹理，例如体积渲染                 |
| `samplerCUBE` | 用于立方体纹理，通常用于处理环境映射等需要立方体贴图的情况   |
| `samplerRECT` | 用于处理矩形纹理，通常用于一些非标准的纹理映射需求           |

声明

```
float a = 1.4;
```

------

## 基础复合数据类型

### 数组

```
int a[4] = {1,2,3,4};
```

### 二维数组

```
int b[2][3] = {{1,2,3},{4,5,6}};
```

------

### 结构体

```
struct v2f{
    int a[4] = {1,2,3,4};
    int b[2][3] = {{1,2,3},{4,5,6}};
}
```

------

## 特殊数据类型

### 向量

向量类型属于CG语言的内置数据类型

内置的向量类型是基于基础数据类型声明的

向量的最大维度不超过4维

数据类型可以是任意数值类型

```
int2 = int2(1,2);
float3 = float3(1.2,3.2,1);
half4 = half4(2,4,1,4);
```

------

### 矩阵

矩阵类型属于CG语言的内置数据类型

矩阵的最大行列不大于4，不小于1

数据类型可以是任意数值类型

```
int2x3 mInt2x3 = {1,2,3
                  4,5,6};
```

------

### bool类型的特殊使用

bool类型同样可以用于如同向量一样声明

它可以用于存储一些逻辑判断结果

```
float3 a = (0.5,0.0,1.0);
float3 b = (0.6,-0.1,0.9);
bool3 c = a < b; //结果为bool3(true,true,false)
```

------

## Swizzle操作符

Swizzle操作符通常以点号(.)的形式使用，后面跟着所需的分量顺序

对于四维向量来说我们可以通过`向量.xyzw`或`向量.rgba`

两种分量的写法来表示向量中的四个值

其中xyzw和rgba分别代表向量中的四个元素

在此的意义就是向量一般可以用来表示坐标和颜色

------

### 如何使用Swizzle操作符

**利用它来提取分量**

```
fixed4 f1 = fixed4(1,2,3,4);
fixed f = f1.w;//xyzw 4
f = f1.a //rgba 4 
```

**利用它来重新排列分量**

```
fixed4 f2 = fixed4(1,2,3,4);
f4 = f2.yzxw; //fixed4(2,3,1,4)
```

**利用它来创建新的向量**

```
 fixed4 f3 = fixed4(1,2,3,4);
 fixed3 f4 = f3.xyz;
 fixed2 f5 = f3.rg;
 fixed4 f5_2 = fixed4(f5,f5);
```

------

### 向量和矩阵的更多用法

**利用向量声明矩阵**

```
fixed4x4 f4x4 = {fixed4(1,2,3,4),
                 fixed4(1,2,3,4),
                 fixed4(1,2,3,4),
                 fixed4(1,2,3,4)};
```

**获取矩阵中的元素**

```
fixed f = f4x4[0][0];
```

**利用向量获取矩阵中的某一行**

```
fixed4 f4 = f4x4[0]
```

**高维转低维**

向量和矩阵都可以用低维存高维，会自动舍去多余元素

```
fixed3x3 f3x3 = f4x4;
```

------

## 运算符

### 比较运算符

| 含义     | 运算符 |
| -------- | ------ |
| 大于     | `>`    |
| 小于     | `<`    |
| 大于等于 | `>=`   |
| 小于等于 | `<=`   |
| 等于     | `==`   |
| 不等于   | `!=`   |

------

### 条件运算符

| 含义       | 运算符                                       |
| ---------- | -------------------------------------------- |
| 三目运算符 | `condition ? value_if_true : value_if_false` |

------

### 逻辑运算符

逻辑运算符在CG中不存在C#中的"短路"操作

| 含义         | 运算符       |
| ------------ | ------------ |
| 逻辑或运算符 | &#124;&#124; |
| 逻辑与运算符 | `&&`         |
| 逻辑非运算符 | `！`         |

------

### 数学运算符

数学运算符在CG中取余符号只能向整数取余

| 含义 | 运算符 |
| ---- | ------ |
| 加法 | `+`    |
| 减法 | `-`    |
| 乘法 | `*`    |
| 除法 | `/`    |
| 取余 | `%`    |
| 自增 | `++`   |
| 自减 | `--`   |

------

## 条件分支语句

CG语法中的流程控制语句和C#中的使用一模一样

需要注意的是

在使用它们时要更多的考虑性能消耗

1. 尽量少的使用循环语句,如果一定要用要减少次数和复杂度
2. 要利用GPU并行性这一特点来替代循环
3. 尽量避免复杂的条件分支

------

## 函数

### 无返回值的函数

```
void name(in 参数类型 参数名, out 参数类型 参数名)
{
    //函数体
}
```

void：以void开头，表示没有返回值

name：函数的名称

in：表示是输入参数，表示由函数外部传递给函数内部，内部不会修改该参数，只会使用该参数进行计算，允许有多个

out：表示是输出参数，表示由函数内部传递给函数的调用者，在函数内部必须对该参数值进行初始化或修改，允许有多个

**注意**

in和out都可以省略，省略后就没有了in和out相关的限制

虽然可以省略，但是建议在编写Shader时不要省略in和out

因为他们可以明确参数的传递方式，提高代码的可读性和可维护性

------

### 有返回值的函数

```
type name(in 参数类型 参数名)
{
    //函数体
    return 返回值;
}
```

type：返回值类型

return：返回指定类型的数据

**注意**

虽然可以在有返回值的函数中使用out参数

但是这并不是常见做法，除非是一些自定义逻辑函数

对于顶点/片元着色器函数只会使用单返回值的方式进行处理

------

CG当中的函数声明和函数使用和C#中基本一致

区别主要是CG中的in和out关键字

1. in表示输入参数，用于外部传给内部，内部只会用，不会改，可以有多个
2. out表示输出参数，用于内部传给外部，内部必须初始化或修改，可以有多个

对于有返回值的函数，要不采用返回值形式，要不采用out

------

## 语义

G语言中提供了 语义 这种特殊关键字用于修饰函数中的传入参数和返回值

它的主要作用是让Shader知道从哪里读取数据，并把数据输出到哪里

让我们在Shader开发当中可以获取到想要的数据，并且可以把数据传递出去

Unity中只支持CG当中的部分语义

```
CGPROGRAM
#pragma vertex myVert
float4 myVert(float4 v:POSITION):SV_POSITION
{
    return UntiyObjectToClipes(v);
}
ENDCG
```

`POSITION`和`SV_POSITION`都是CG语言的语义

`POSITION`把模型的顶点坐标填充输入到参数v中

`SV_POSITION`顶点着色器输出的内容是裁剪空间中的顶点坐标

------

### 应用阶段—>顶点着色器

应用阶段传递模型数据给顶点着色器时Unity支持的语义

一般在顶点着色器回调函数的传入参数中应用

**常用语义**

| 语义             | 含义                                                         |
| ---------------- | ------------------------------------------------------------ |
| `POSITION`       | 模型空间中的顶点位置，通常是float4类型                       |
| `NORMAL`         | 顶点法线，通常是float3类型                                   |
| `TANGENT`        | 顶点切线，通常是float4类型                                   |
| `TEXCOORD0,1...` | 该顶点的纹理坐标，通常是float2或者float4类型,TEXCOORD0表示第一组纹理坐标，依次类推(纹理坐标：也称UV坐标，表示该顶点对应纹理图像上的位置) |
| `COLOR`          | 顶点颜色，通常是fixed4或float4类型                           |

------

### 顶点着色器—>片元着色器

从顶点着色器传递数据给片元着色器时Unity支持的语义

一般在顶点着色器回调函数的返回值中应用

**常用语义**

| 语义                    | 含义                                     |
| ----------------------- | ---------------------------------------- |
| `SV_POSITION`           | 裁剪空间中的顶点坐标（必备）             |
| `COLOR0`                | 通常用于输出第一组顶点颜色（不是必须的） |
| `COLOR1`                | 通常用于输出第二组顶点颜色（不是必须的） |
| `TEXCOORD0`~`TEXCOORD7` | 通常用于输出纹理坐标（不是必须的）       |

------

### 片元着色器输出

片元着色器输出时Unity支持的常用语义，一般在片元着色器回调函数的返回值中应用。

**常用语义：**

| 语义        | 含义                     |
| ----------- | ------------------------ |
| `SV_Target` | 输出值会存储到渲染目标中 |

------

### 更多语义

HLSL语义汇总：https://learn.microsoft.com/zh-cn/windows/win32/direct3dhlsl/dx-graphics-hlsl-semantics?redirectedfrom=MSDN

------

## 顶点/片元着色器传递更多参数

当我们在顶点/片元着色器当中想要获取更多模型相关信息时

可以使用结构体对数据进行封装

从而实现传递更多参数

**声明用于传递给顶点着色器函数的结构体a2v**

```
struct a2v
{
    //顶点坐标(基于模型空间)
    float4 vertex:POSITION;
    //顶点法线(基于模型空间)
    float3 normal:NORMAL;
    //纹理坐标(uv坐标)
    float2 uv:TEXCOORD0;
};
```

**声明用于传递给片元着色器的结构体v2f**

```
struct v2f
{
    //裁剪空间下的坐标
    float4 position:SV_POSITION;
    //顶点法线(基于模型空间)
    float3 normal:NORMAL;
    //纹理坐标(uv坐标)
    float2 uv:TEXCOORD0;
};
```

**完整实例**

```
CGPROGRAM
#pragma vertex myVert
#pragma fragment frag

//该结构体 是用于从应用阶段获取语义数据后 传递给顶点着色器回调函数的
struct a2v
{
    //顶点坐标(基于模型空间)
    float4 vertex:POSITION;
    //顶点法线(基于模型空间)
    float3 normal:NORMAL;
    //纹理坐标(uv坐标)
    float2 uv:TEXCOORD0;
};
//从顶点着色器传递给片元着色器的 结构体数据 
//同样这里面的成员也需要用语义去进行修饰
struct v2f
{
    //裁剪空间下的坐标
    float4 position:SV_POSITION;
    //顶点法线(基于模型空间)
    float3 normal:NORMAL;
    //纹理坐标(uv坐标)
    float2 uv:TEXCOORD0;
};
v2f vert(a2v data)
{
    //需要传递给片元着色器的数据
    v2f v2fData;
    v2fData.position = UnityObjectToClipPos(data.vertex);
    v2fData.normal = data.normal;
    v2fData.uv = data.uv;
    return v2fData;
}

fixed4 frag(v2f data):SV_Target
{
    return fixed4(0,1,0,1);
}

ENDCG
```

------

## **ShaderLab属性类型和变量类型的匹配关系**

ShaderLab中声明的属性都是需要在Shader(着色器)逻辑中使用的

需要在CG中声明和属性对应类型的同名变量 才可以使用

```
Shader "ShaderName"{
    Properties{
        _Color("BackGroundColor", Color) = (1,1,1,1)
    }
    SubShader{
        Pass{
            CGPROGRAM
            //使用属性
            float4 _Color;
            ENDCG
        }
    }
    Fallback "VertexLit"
}
```

**CG中变量类型的对应ShaderLab的属性类型**

| ShaderLab属性类型     | CG变量类型                |
| --------------------- | ------------------------- |
| `Color`,`Vector`      | `float4`,`half4`,`fixed4` |
| `Range`,`Float`,`Int` | `float`,`half`,`fixed`    |
| `2D`                  | `sampler2D`               |
| `Cube`                | `samplerCube`             |
| `3D`                  | `sampler3D`               |
| `2DArray`             | `sampler2DArray`          |

------

## CG内置函数

Unity Shader中的CG语言提供了各种用于图形编程的函数

这些函数是CG为我们封装好的逻辑我们可以使用它们来编写Unity Shader

------

### 数学函数

**三角函数相关**

| 函数                            | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| `sincos(float x, out s, out c)` | 该函数同时计算x的sin值和cos值通过s和c进行返回（比分别运算快很多） |
| `sin(x)`                        | 正弦函数                                                     |
| `cos(x)`                        | 余弦函数                                                     |
| `tan(x)`                        | 正切函数                                                     |
| `sinh(x)`                       | 双曲正弦函数                                                 |
| `cosh(x)`                       | 双曲余弦函数                                                 |
| `tanh(x)`                       | 双曲正切函数                                                 |
| `asin(x)`                       | 反正弦函数，输入参数范围[-1,1]，返回[-π/2,π/2]区间的角度值   |
| `acos(x)`                       | 反余弦函数，输入参数范围[-1,1]，返回[0,π]区间的角度值        |
| `atan(x)`                       | 反正切函数，输入参数范围[-1,1]，返回[-π/2,π/2]区间的角度值   |
| `atan2(y,x)`                    | 计算y/x的反正切值。和atan功能一样，只是输入参数不同。atan(x)=atan2(x,1) |

------

**向量、矩阵相关**

| 函数             | 说明                                 |
| ---------------- | ------------------------------------ |
| `cross(A,B)`     | 叉乘（注意：传入向量必须是三维向量） |
| `dot(A,B)`       | 点乘（注意：传入向量必须是三维向量） |
| `mul(M,N)`       | 计算两个矩阵相乘                     |
| `mul(M,v)`       | 计算矩阵和向量相乘                   |
| `mul(v,M)`       | 计算向量和矩阵相乘                   |
| `transpose(M)`   | M为矩阵，计算M的转置矩阵             |
| `determinant(m)` | 计算矩阵的行列式因子                 |

------

**数值相关**

| 函数                    | 说明                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `abs(x)`                | 返回输入参数的绝对值                                         |
| `ceil(x)`               | 对输入参数向上取整                                           |
| `floor(x)`              | 对输入参数向下取整                                           |
| `clamp(x,a,b)`          | 如果x小于a，则返回a；x大于b，则返回b；否则，返回x（"夹紧"函数） |
| `radians(x)`            | 角度转弧度                                                   |
| `degrees(x)`            | 弧度转角度                                                   |
| `max(a,b)`              | 返回最大值                                                   |
| `min(a,b)`              | 返回最小值                                                   |
| `sqrt(x)`               | 求x的平方根，x必须大于0                                      |
| `exp(x)`                | 计算e的x次方的值，e=2.71828182845904523536                   |
| `exp2(x)`               | 计算2的x次方的值                                             |
| `fmod(x,y)`             | 返回x/y的余数，y不为0                                        |
| `frac(x)`               | 返回标量或每个矢量分量的小数部分                             |
| `frexp(x,out)`          | 将浮点数x分解为尾数和直属，即 x = m * 2的exp次方，返回m，将指数存储exp中 |
| `isfinite(x)`           | 判断标量或者向量中的每个数据是否是有限数，如果是返回true，否则返回false |
| `isinf(x)`              | 判断标量或者向量中的每个数据是否是无限，如果是返回true，否则返回false |
| `isnan(x)`              | 判断标量或者向量中的每个数据是否是非数据，如果是返回true，否则返回false |
| `ldexp(x,n)`            | 计算x * 2的n次方 的值                                        |
| `log(x)`                | 计算ln(x)的值，x必须大于0                                    |
| `log2(x)`               | 计算log2(x次方)的值，x必须大于0                              |
| `log10(x)`              | 计算log2(x次方)的值，x必须大于0                              |
| `saturate(x)`           | 如果x小于0，返回0；如果x大于1，返回1；否则，返回x            |
| `sign(x)`               | 如果x大于0，返回1；如果x小于0，返回-1；否则，返回0           |
| `smoothstep(min,max,x)` | 值x位于min、max区间内，如果x=min，返回0；如果x=max，返回1；如果在两者之间，返回-2* ((x-min)/(max -  min))的三次方 + 3* ((x - min)/(max - min))的2次方 |
| `step(a,x)`             | 如果x<a,返回0；否则，返回1                                   |
| `all(x)`                | 输入参数均不为0，则返回true;否则返回False。相当于逻辑与&&    |
| `any(x)`                | 输入参数只要有其中一个不为0，则返回true。相当于逻辑或&#124;&#124; |

------

**其他**

| 函数                 | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| `lit(NdotL,NdotH,m)` | N表示法向量；L表示入射光向量；H表示半角向量；m表示高光系数；该函数计算环境光、散射光、镜面光的贡献，返回4维向量；x位表示环境光贡献；y位表示散射光贡献；z位表示镜面光贡献；w始终为1 |
| `noise(x) `          | 噪声函数，返回值始终是0~1之间；对于相同的输入，始终返回相同值，不是真正意义的随机噪声 |

------

### 几何函数

| 函数               | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| `length(v)`        | 返回一个向量的模长                                           |
| `normalize(v)`     | 归一化向量                                                   |
| `distance(p1,p2)`  | 计算两点之间的距离                                           |
| `reflect(I,N)`     | 计算反射光方向向量，I为入射光，N为顶点法向量，。I是指向顶点的，I和N必须被归一化，必须是3维向量 |
| `refract(I,N,eta)` | 计算折射向量，I为入射光，N为顶点法向量，eta为折射系数。I是指向顶点的，I和N必须被归一化，必须是3维向量 |

------

### 纹理函数

注意：这些纹理采样函数返回值为 fixed4 类型的颜色值

**二维纹理**

| 函数                                                         | 说明                                     |
| ------------------------------------------------------------ | ---------------------------------------- |
| `tex2D(sampler2D tex, float2 s) `                            | 二维纹理查询                             |
| `tex2D(sampler2D tex, float2 s, float2 dsdx, float2 dsdy)`   | 使用导数值查询二维纹理                   |
| `tex2D(sampler2D tex, float3 sz)`                            | 二维纹理查询，并进行深度值比较           |
| `tex2D(sampler2D tex, float3 sz, float2 dsdx, float2 dsdy) ` | 使用导数值查询二维纹理，并进行深度值比较 |
| `tex2Dproj(sampler2D tex, float3 sq)`                        | 二维投影纹理查询                         |
| `tex2Dproj(sampler2D tex, float4 szq)  `                     | 二维投影纹理查询，并进行深度值比较       |

------

**立方体纹理**

| 函数                                                         | 说明                                 |
| ------------------------------------------------------------ | ------------------------------------ |
| `texCUBE(samplerCUBE tex, float3 s)`                         | 查询立方体纹理                       |
| `texCUBE(samplerCUBE tex, float3 s, float3 dsdx, float3 dsdy)` | 结合导数值查询立方体纹理             |
| `texCUBEDproj(samplerCUBE tex, float4 sq)`                   | 查询立方体投影纹理，并进行深度值比较 |

------

**其他纹理**

| 函数名称                                                     | 描述                                     |
| ------------------------------------------------------------ | ---------------------------------------- |
| `tex1D(sampler1D tex, float s)`                              | 一维纹理查询                             |
| `tex1D(sampler1D tex, float s, float dsdx, float dsdy)`      | 使用导数值查询一维纹理                   |
| `tex1D(sampler1D tex, float2 sz)`                            | 一维纹理查询，并进行深度值比较           |
| `tex1D(sampler1D tex, float2 sz, float dsdx, float dsdy)`    | 使用导数值查询一维纹理，并进行深度值比较 |
| `tex1Dproj(sampler1D tex, float2 sq)`                        | 一维投影纹理查询                         |
| `tex1Dproj(sampler1D tex, float3 szq)`                       | 一维投影纹理查询，并进行深度值比较       |
| `texRECT(samplerRECT tex, float2 s)`                         | 矩形纹理查询                             |
| `texRECT(samplerRECT tex, float2 s, float2 dsdx, float2 dsdy)` | 使用导数值查询矩形纹理                   |
| `texRECT(samplerRECT tex, float3 sz)`                        | 矩形纹理查询，并进行深度值比较           |
| `texRECT(samplerRECT tex, float3 sz, float2 dsdx, float2 dsdy)` | 使用导数值查询矩形纹理，并进行深度值比较 |
| `texRECTproj(samplerRECT tex, float3 sq)`                    | 矩形投影纹理查询                         |
| `texRECTproj(samplerRECT tex, float3 szq)`                   | 矩形投影纹理查询，并进行深度值比较       |
| `tex3D(sampler3D tex, float3 s)`                             | 查询三维纹理                             |
| `tex3D(sampler3D tex, float3 s, float3 dsdx, float3 dsdy)`   | 结合导数值查询三维纹理                   |
| `tex3DDproj(sampler3D tex, float4 sq)`                       | 查询三维投影纹理，并进行深度值比较       |

------

## CG内置文件

我们可以在Unity的安装目录中找到CG内置文件

在Editor—>Data—>CGIncludes中

- 后缀为cginc的文件为CG语言内置文件
- 后缀为glslinc的文件为GLSL语言内置文件

他们是预定义的Shader文件，里面包含了一些已经写好的Shader相关逻辑

作用和CG内置函数一样，可以提升我们的Shader开发效率

可以直接使用其中的方法等内容来进行逻辑开发

**常用的内置文件**

| 文件名                       | 描述                                             |
| ---------------------------- | ------------------------------------------------ |
| `UnityCG.cginc`              | 包含最常用的帮助函数、宏和结构体等               |
| `Lighting.cginc`             | 包含各种内置光照模型（自动包含于Surface Shader） |
| `UnityShaderVariables.cginc` | 包含许多内置的全局变量，在编译时自动包含         |
| `HLSLSupport.cginc`          | 声明了很多用于跨平台编译的宏和定义               |

------

### 使用CG内置文件

在CG语句块中进行引用

通过编译指令

```
#include "内置文件名.cginc
```

我们便可以在CG语言中使用其中的内容

注意：一些常用的函数、宏、变量，可以不用引用，Unity会在编译时自动识别

但是为了避免报错，建议都引用

"宏"通常指的是一种预处理指令或代码片段，用于在代码中进行文本替换。

宏允许程序员定义一个标识符（通常以大写字母表示）来代表一个代码片段，

然后在编译时将这个标识符替换为相应的代码，这种替换过程称为宏展开。

------

### 常用内容

**常用方法（来自 `UnityCG.cginc`）**

| 方法名                                         | 描述                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| `float3 WorldSpaceViewDir(float4 v)`           | 输入模型空间中的顶点位置，返回世界空间中从该点到摄像机的观察方向 |
| `float3 ObjSpaceViewDir(float4 v)`             | 输入模型空间中的顶点位置，返回模型空间中从该点到摄像机的观察方向 |
| `float3 WorldSpaceLightDir(float4 v)`          | 输入模型空间中的顶点位置，返回世界空间中从该点到光源的光照方向（未归一化） |
| `float3 ObjSpaceLightDir(float4 v)`            | 输入模型空间中的顶点位置，返回模型空间中从该点到光源的光照方向（未归一化） |
| `float3 UnityObjectToWorldNormal(float3 norm)` | 将法线方向从模型空间转换到世界空间中                         |
| `float3 UnityObjectToWorldDir(in float3 dir)`  | 将方向矢量从模型空间转换到世界空间中                         |
| `float3 UnityWorldToObjectDir(float3 dir)`     | 将方向矢量从世界空间转换到模型空间中                         |

------

**常用结构体（来自 `UnityCG.cginc`）**

| 结构体名       | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `appdata_base` | 用于顶点着色器输入，包括顶点位置、顶点法线、第一组纹理坐标   |
| `appdata_tan`  | 用于顶点着色器输入，包括顶点位置、顶点法线、顶点切线、第一组纹理坐标 |
| `appdata_full` | 用于顶点着色器输入，包括顶点位置、顶点法线、顶点切线、四组（或更多）纹理坐标 |
| `appdata_img`  | 用于顶点着色器输入，仅包含顶点位置和第一组纹理坐标           |
| `v2f_img`      | 用于顶点着色器输出，包含裁剪空间中的位置和纹理坐标           |

------

 **变换矩阵宏（来自 `UnityShaderVariables.cginc`）**

坐标空间变换顺序

模型空间 -> 世界空间 -> 观察空间 -> 裁剪空间 -> 屏幕空间

| 宏名                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| `UNITY_MATRIX_MVP`   | 当前的模型*观察*投影矩阵，用于将顶点/方向向量从模型空间变换到裁剪空间 |
| `UNITY_MATRIX_MV`    | 当前的模型*观察矩阵，用于将顶点/方向向量从模型空间变换到观察空间 |
| `UNITY_MATRIX_V`     | 当前的观察矩阵，用于将顶点/方向向量从世界空间变换到观察空间  |
| `UNITY_MATRIX_P`     | 当前的投影矩阵，用于将顶点/方向向量从观察空间变换到裁剪空间  |
| `UNITY_MATRIX_VP`    | 当前的观察*投影矩阵，用于将顶点/方向向量从世界空间变换到裁剪空间 |
| `UNITY_MATRIX_T_MV`  | `UNITY_MATRIX_MV`的转置矩阵                                  |
| `UNITY_MATRIX_IT_MV` | `UNITY_MATRIX_MV`的逆转置矩阵，用于将法线从模型空间变换到观察空间 |
| `_Object2World`      | 当前的模型矩阵，用于将顶点/方向矢量从模型空间变换到世界空间  |
| `_World2Object`      | `_Object2World`的逆矩阵，用于将顶点/方向矢量从世界空间变换到模型空间 |

------

**常用变量**

| 变量名         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `_Time`        | 自关卡加载以来的时间，用于对着色器内的事物进行动画处理       |
| `_LightColor0` | 光源的颜色（向前渲染时，UnityLightingCommon.cginc；延迟渲染，UnityDeferredLibrary.cginc） |

------

CG中的内置文件和内置函数一样，都是用于帮助我们进行Shader开发的。  
通过利用其中的函数、宏、全局变量等内容，我们可以提高Shader开发的效率。  
建议将常用的内置文件、函数、宏、变量等内容记到笔记中，方便查阅。  
更多内置文件和函数的资料可以参考Unity官网的相关文档：

- [内置文件相关](https://docs.unity3d.com/Manual/SL-BuiltinIncludes.html)
- [函数相关](https://docs.unity3d.com/Manual/SL-BuiltinFunctions.html)
- [宏相关](https://docs.unity3d.com/Manual/SL-BuiltinMacros.html)
- [变量相关](https://docs.unity3d.com/Manual/SL-UnityShaderVariables.html)

