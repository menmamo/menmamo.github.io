---
title: "纹理贴图叠加BlinnPhong光照模型"
author: "mona"
date: 2025-08-15T00:21:17+08:00
categories: ["Unity"]
tags: ["shader"]
---

## 单张纹理结合BlinnPhong光照模型

在计算时，有以下的3点注意点

1. 纹理颜色需要和漫反射颜色 进行乘法叠加  作为新的漫反射颜色
2. 计算兰伯特光照时 要使用 1的结果 作为漫反射颜色
3. 最终和环境光叠加时，为了避免渲染效果发灰 环境光变量和1中结果（新的漫反射颜色）相乘

------

## 片元着色器实现

```
Shader "Unlit/uvlearn2"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _MainColor("MainColor",COLOR) = (1,1,1,1)
        _HighLightColor("HighLight",COLOR) = (1,1,1,1)
        _HighLightNum("HighNum",Range(0.1,20)) = 5
    }
    SubShader
    {

        Pass
        {
            Tags{ "LightMode"="ForwardBase"}

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            sampler2D _MainTex;
            float4 _MainTex_ST;
            fixed4 _MainColor;
            fixed4 _HighLightColor;
            float _HighLightNum;

            struct v2f
            {
                float4 pos:SV_POSITION;
                float3 normal:NORMAL;
                float4 wpos:TEXCOORD1;
                half2 uv: TEXCOORD0;
            };

            v2f vert (appdata_base v)
            {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.normal = UnityObjectToWorldNormal(v.normal);
                o.wpos = mul(unity_ObjectToWorld,v.vertex);
                o.uv = TRANSFORM_TEX(v.texcoord.xy,_MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                //uv取色和漫反射颜色相乘
                fixed3 uvColor = tex2D(_MainTex,i.uv).rgb * _MainColor.rgb;
                //归一化法向量
                float3 normal = normalize(i.normal);
                //Lambert
                float3 cameraDir = normalize(_WorldSpaceCameraPos.xyz);
                fixed3 lamBertColor = _LightColor0.rgb * uvColor.rgb * max(0,dot(cameraDir,normal));//叠加uvcolor
                //BlinnPhong
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                float3 viewDir = normalize(UnityWorldSpaceViewDir(i.wpos).xyz);
                float3 halfA = normalize(lightDir + viewDir);
                fixed3 BlinnPhongColor = _LightColor0.rgb * _HighLightColor.rgb * pow(max(0,dot(halfA,normal)),_HighLightNum);
                //颜色叠加
                fixed3 color = UNITY_LIGHTMODEL_AMBIENT * uvColor + lamBertColor + BlinnPhongColor;
                return fixed4(color,1);
            }
            ENDCG
        }
    }
}
```

