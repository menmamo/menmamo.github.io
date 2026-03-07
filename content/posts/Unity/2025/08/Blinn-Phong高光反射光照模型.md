---
title: "Blinn-Phong高光反射光照模型"
author: "mona"
date: 2025-08-12T00:23:00+08:00
categories: ["Unity"]
tags: ["shader"]
---

## Blinn-Phong式高光反射光照模型的来历和原理

Blinn-Phong式高光反射光照模型 是 Blinn-Phong光照模型的一部分

Blinn-Phone光照模型的提出者：

吉姆·布林（Jim Blinn，美国计算机科学家）

**原理:**

Blinn-Phong式高光反射光照模型的理论是

它是对Phong式高光反射光照模型的改进

它不再使用反射向量计算镜面反射，而是使用半角向量来进行计算

半角向量为视角方向和灯光方向的角平分线方向

认为高光反射的颜色和 顶点法线向量以及半角向量夹角的余弦成正比

并且通过对余弦值取n次幂来表示光泽度（或反光度）

------

## Blinn-Phong式高光反射光照模型的公式

高光反射光照颜色 = 光源的颜色 * 材质高光反射颜色 * max（0, 标准化后顶点法线方向向量 · 标准化后半角向量方向向量）幂

1. 标准化后顶点法线方向向量 · 标准化后半角向量方向向量 得到的结果就是 cosθ
2. 半角向量方向向量 = 视角单位向量 + 入射光单位向量
3. 幂 代表的是光泽度  余弦值取n次幂

------

## Phong和Blinn-Phong的区别

由于两个光照模型中高光反射光照模型的计算方式不一样

从而会带来一些表现上的不同

**高光散射**

Blinn-Phong模型的高光通常会产生相对均匀的高光散射，这会使物体看起来光滑而均匀。

Phong模型的高光可能会呈现更为锐利的高光散射，因为它基于观察者和光源之间的夹角。

这可能导致一些区域看起来特别亮，而另一些区域则非常暗。

**高光锐度**

Blinn-Phong模型的高光通常具有较广的散射角，因此看起来不那么锐利。

Phong模型的高光可能会更加锐利，特别是在观察者和光源夹角较小时，可能表现为小而亮的点。

**光滑度和表面纹理**

Blinn-Phong模型通常更适合表现光滑的表面，因为它考虑了表面微观凹凸之间的相互作用，使得光照在表面上更加均匀分布。

Phong模型有时更适合表现具有粗糙表面纹理的物体，因为它的高光散射可能会使纹理和细节更加突出。

**镜面高光大小**

Blinn-Phong模型通常产生的镜面高光相对较大，但均匀分布。

Phong模型可能会产生较小且锐利的镜面高光。

------

## 顶点着色器实现

```
Shader "Unlit/BlinnPhong"
{
    Properties
    {
        _HighLightColor ("HighLightColor", COLOR) = (1,1,1,1)
        _HighLightNum("HighLightNum", Range(0,20)) = 5
    }
    SubShader
    {

        Pass
        {
            Tags{ "LightMode" = "ForwardBase"}

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            struct v2f
            {
                float4 vertex : SV_POSITION;
                float3 normal : NORMAL;
                fixed3 color : COLOR;
            };

            float4 _HighLightColor;
            float _HighLightNum;

            v2f vert (appdata_base v)
            {
                v2f data;
                data.vertex = UnityObjectToClipPos(v.vertex);
                data.normal = UnityObjectToWorldNormal(v.normal);
                float3 wpos = mul(unity_ObjectToWorld,v.vertex).xyz;
                float3 viewDir = normalize(_WorldSpaceCameraPos - wpos);
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                float3 halfDir = normalize(viewDir + lightDir);
                float3 wnormal = UnityObjectToWorldNormal(v.normal);
                data.color = _LightColor0 * _HighLightColor * pow(max(0,dot(wnormal,halfDir)),_HighLightNum);
                return data;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                return fixed4(i.color,1);
            }
            ENDCG
        }
    }
}

```

------

## 片元着色器实现

```
Shader "Unlit/BlinnPhongF"
{
    Properties
    {
        _HighLightColor ("HighLightColor", COLOR) = (1,1,1,1)
        _HighLightNum("HighLightNum", Range(0,20)) = 5
    }
    SubShader
    {

        Pass
        {
            Tags{ "LightMode" = "ForwardBase" }
 
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag


            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            struct v2f
            {
                float4 vertex : SV_POSITION;
                float3 normal : NORMAL;
                fixed3 color : COLOR;
                float4 wpos : TEXCOORD0;
            };

            float4 _HighLightColor;
            float _HighLightNum;

            v2f vert (appdata_base v)
            {
                v2f data;
                data.vertex = UnityObjectToClipPos(v.vertex);
                data.normal = UnityObjectToWorldNormal(v.normal);
                data.wpos = mul(unity_ObjectToWorld,v.vertex);
                return data;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                float3 normal = normalize(i.normal);
                float3 viewPos = normalize (_WorldSpaceCameraPos - i.wpos).xyz;
                float3 lightPos = normalize(_WorldSpaceLightPos0).xyz;
                float3 halfDir = normalize(viewPos + lightPos);
                fixed3 color = _LightColor0 * _HighLightColor * pow(max(0 , dot(normal,halfDir)) ,_HighLightNum);
                return fixed4(color,1);
            }
            ENDCG
        }
    }
}

```

