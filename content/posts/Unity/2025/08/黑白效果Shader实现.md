---
title: "黑白效果Shader实现"
author: "mona"
date: 2025-08-13T23:03:02+08:00
categories: ["Unity"]
tags: ["shader"]
---

我们可以通过将颜色值点乘`float3(0.299, 0.587, 0.114)`将颜色转为黑白

`float3(0.299, 0.587, 0.114)`这三个系数正好是 **人眼感知亮度** 的权重（ITU-R BT.601 标准），用来把 RGB 颜色转换为亮度值（luminance / 灰度值）。

而这三个数字，是科学家用光谱数据和视觉实验算出来的，保证在 **看起来的亮度** 上最符合人眼感知。

## Shader实现

```
Shader "Unlit/gray"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _Grayscale("Grayscale", Range(0, 1)) = 0
    }
    SubShader
    {

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            sampler2D _MainTex;
            float4 _MainTex_ST;
            float _Grayscale;
            v2f_img vert (appdata_base v)
            {
                v2f_img o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.texcoord.xy,_MainTex);
                return o;
            }

            fixed4 frag (v2f_img i) : SV_Target
            {
                fixed4 color = tex2D(_MainTex,i.uv);
                fixed4 grayColor = dot(color,float3(0.299, 0.587, 0.114));
                color = lerp(color,grayColor,_Grayscale);       
                return color;
            }
            ENDCG
        }
    }
}

```

