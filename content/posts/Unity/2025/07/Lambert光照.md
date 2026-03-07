---
title: "Lambert光照"
author: "mona"
date: 2025-07-02T14:42:00+08:00
categories: ["Unity"]
tags: ["shader"]
---

## 两个颜色相乘

两个颜色变量相乘，通常用于计算光照、材质混合、纹理混合等等需求

它是一种用于混合颜色的操作

计算结果可以理解为两种颜色叠加在一起的效果

在颜色相乘中，通常是使用颜色通道的值来执行逐通道的乘法

颜色A * 颜色B = A、B颜色叠加

------

## 漫反射基本概念

漫反射（Diffuse Reflection）是光线撞击一个物体表面后以各个方向均匀地反射出去的过程

在漫反射下，光线以无规律的方式散射，而不像镜面反射那样按照特定的角度反射。

这种散射导致了物体表面看起来均匀而不闪烁的效果。

------

## 兰伯特光照模型的来历和原理

兰伯特(Lambert)光照模型，也称为朗伯反射模型

是由瑞士数学家约翰·海因里希·朗伯（Johann Heinrich Lambert）于1760年左右首次提出

朗伯是18世纪的一个杰出科学家，他在光学和数学领域作出了众多贡献。

兰伯特光照模型描述了漫反射表面对光线的反射行为，它成为计算机图形学和渲染中重要的基础模型之一

兰伯特光照模型的理论是
认为漫反射光的强度仅与入射光的方向和反射点处表面法线的夹角的余弦成正比

-----

## 兰伯特光照模型的公式

公式：

漫反射光照颜色 = 光源的颜色 * 材质的漫反射颜色 * max（0, 标准化后物体表面法线向量· 标准化后光源方向向量）

其中：

1. 标准化后物体表面法线向量· 标准化后光源方向向量 得到的结果就是 cosθ
2. max(0,cosθ)的目的是避免负数，对于模型背面的部分，认为找不到光，直接乘以0，变为黑色

------

## 兰伯特光照模型实现

### 逐顶点光照

```
Shader "Unlit/Lambert"
{
    Properties
    {
         _MainColor("MainColor", Color) = (1,1,1,1)
    }
    SubShader
    {
        Tags {"LightMode"="ForwardBase" }

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            fixed4 _MainColor;
                
            struct v2f
            {
                float4 pos:SV_POSITION;
                float3 color:COLOR;
            };

            v2f vert(appdata_base v)
            {
                v2f v2fData;
                //将模型坐标系下的顶点坐标转换为裁剪坐标系下的顶点坐标
                v2fData.pos = UnityObjectToClipPos(v.vertex);
                //获得世界坐标系下的法线信息
                float3 normal = UnityObjectToWorldNormal(v.normal);
                //归一化世界坐标下的光源向量
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                //计算兰伯特光照
                v2fData.color = _LightColor0.rgb * _MainColor.rgb * max(0, dot(normal, lightDir));
                return v2fData;
            }

            fixed4 frag(v2f i):SV_Target
            {
                return fixed4(i.color.rgb,1);
            }
            ENDCG
        }
    }
}
```

### 逐片元光照

```
Shader "Unlit/Lambert"
{
    Properties
    {
         _MainColor("MainColor", Color) = (1,1,1,1)
    }
    SubShader
    {
        Tags {"LightMode"="ForwardBase" }

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            fixed4 _MainColor;
                
            struct v2f
            {
                float4 pos:SV_POSITION;
                float3 normal:NORMAL;
            };

            v2f vert(appdata_base v)
            {      
                v2f v2fData;
                v2fData.pos = UnityObjectToClipPos(v.vertex);
                v2fData.normal = UnityObjectToWorldNormal(v.normal);
                return v2fData;
            }

            fixed4 frag(v2f i):SV_Target
            {       
                fixed3 lightDir = normalize(_WorldSpaceLightPos0);
                fixed3 color = _LightColor0 * _MainColor * max(0,dot(i.normal,lightDir));
                return fixed4(color.rgb,1);
            }
            ENDCG
        }
    }
}

```

------

## 半兰伯特光照

根据数学公式我们知道

假设单位向量A和单位向量B，它们的点乘结果为

A·B = |A| * |B| * cosθ = cosθ

cosθ 的范围在 - 1 到 1 之间（夹角为0度时,点乘结果为1;当夹角为180度时,点乘结果为-1）

因此单位向量进行点乘时

它们的结果是 -1 ~ 1

**半兰伯特光照模型的公式**

漫反射光照颜色 = 光源的颜色 * 材质的漫反射颜色 *（（标准化后物体表面法线向量· 标准化后光源方向向量）* 0.5 + 0.5）

（（标准化后物体表面法线向量· 标准化后光源方向向量）* 0.5 + 0.5）点乘小于0的部分都会变成 0 ~ 0.5

 -1 ~ 1  映射到了 0 ~ 1

------

## 半兰伯特光照实现

### 逐顶点光照

```
Shader "Unlit/Lambert2"
{
    Properties
    {
        _mainColor("MainColor",Color) = (1,1,1,1)
    }
    SubShader
    {
        Tags{ "LightMode"="ForwardBase" }

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            fixed4 _mainColor;

            struct v2f
            {
                float4 pos:SV_POSITION;
                fixed4 color:COLOR;
            };

            v2f vert(appdata_base v)
            {
                v2f v2fData;
                //从模型空间转换到裁剪空间
                v2fData.pos = UnityObjectToClipPos(v.vertex);
                //得到世界空间下的法线信息
                float3 normal = UnityObjectToWorldNormal(v.normal);
                float3 lightDir = normalize(_WorldSpaceLightPos0).xyz;
                v2fData.color = _LightColor0 * _mainColor * (0.5 * dot(normal,lightDir) + 0.5);
                return v2fData;
            }

            fixed4 frag(v2f v):SV_Target
            {
                return fixed4 (v.color.xyz,1);
            }

            ENDCG
        }
    }
}
```

------

### 逐片元光照

```
Shader "Unlit/Lambert2"
{
    Properties
    {
        _mainColor("MainColor",Color) = (1,1,1,1)
    }
    SubShader
    {
        Tags{ "LightMode"="ForwardBase" }

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            fixed4 _mainColor;

            struct v2f
            {
                float4 pos:SV_POSITION;
                fixed3 normal:NORMAL;
            };

            v2f vert(appdata_base v)
            {
                v2f v2fData;
                //从模型空间转换到裁剪空间
                v2fData.pos = UnityObjectToClipPos(v.vertex);
                //得到世界空间下的法线信息
                v2fData.normal = UnityObjectToWorldNormal(v.normal);
                return v2fData;
            }

            fixed4 frag(v2f v):SV_Target
            {
                //得到归一话的灯光向量
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                //半兰伯特光照（0.5* dot(法向量,灯光向量)+0.5）
                fixed3 color = _mainColor.rgb * _LightColor0.rgb * (0.5 * dot(v.normal,lightDir) + 0.5); 
                return fixed4 (color.rgb,1);
            }
            ENDCG
        }
    }
}
```

