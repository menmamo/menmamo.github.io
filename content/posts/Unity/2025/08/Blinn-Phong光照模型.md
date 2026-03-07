---
title: "Blinn-Phong光照模型"
author: "mona"
date: 2025-08-12T22:32:56+08:00
categories: ["Unity"]
tags: ["shader"]
---

## Blinn Phong光照模型的公式

物体表面光照颜色 = 环境光颜色 + 漫反射光颜色 + 高光反射光颜色

其中：

环境光颜色 = UNITY_LIGHTMODEL_AMBIENT(unity_AmbientSky、unity_AmbientEquator、unity_AmbientGround)

漫反射光颜色 = 兰伯特光照模型 计算得到的颜色

高光反射光颜色 = Blinn Phong式高光反射光照模型 计算得到的颜色

------

## 顶点着色器实现

```
Shader "Unlit/Blinn_Phong"
{
    Properties
    {
        _MainColor("MainColor",COLOR) = (1,1,1,1)
        _HighLightColor("HighLightColor",COLOR) = (1,1,1,1)
        _HighLightNum("HighLightNum",Range(0.1,20)) = 10
       
    }
    SubShader
    {

        Pass
        {
            Tags{"LightMode"="ForwardBase" }

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag


            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            struct v2f
            {
                float4 pos : SV_POSITION;
                float3 normal : NORMAL;
                fixed3 color : COLOR;
            };


            fixed4 _MainColor;
            fixed4 _HighLightColor;
            float _HighLightNum;

            fixed3 getLembertColor(in float3 wnormal)
            {
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                fixed3 color = _LightColor0.rgb * _MainColor.rgb * max(0,dot(wnormal,lightDir));
                return color;
            } 

            fixed3 getBlinnPhongColor(in float3 wpos, in float3 wnormal)
            {
                float3 viewDic = normalize(_WorldSpaceCameraPos.xyz - wpos);
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                float3 halfDir = normalize(viewDic + lightDir);
                fixed3 color = _LightColor0.rgb * _HighLightColor.rgb * pow(max(0,dot(wnormal,halfDir)),_HighLightNum);
                return color;
            }

            v2f vert (appdata_base v)
            {
                v2f data;
                data.pos = UnityObjectToClipPos(v.vertex);
                data.normal = UnityObjectToWorldNormal(v.normal);
                //计算兰伯特光照
                float3 wnormal = normalize(data.normal);
                fixed3 lembertColor = getLembertColor(wnormal);
                //计算BlinnPhong高光模型
                float3 wpos = mul(unity_ObjectToWorld,v.vertex);
                fixed3 blinnPhongColor = getBlinnPhongColor(wpos,wnormal);
                //BlinnPhong光照模型
                data.color = UNITY_LIGHTMODEL_AMBIENT.rbg + lembertColor + blinnPhongColor;
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
Shader "Unlit/Blinn_PhongF"
{
    Properties
    {
        _MainColor("MainColor",COLOR) = (1,1,1,1)
        _HighLightColor("HighLightColor",COLOR) = (1,1,1,1)
        _HighLightNum("HighLightNum",Range(0.1,20)) = 10
    }
    SubShader
    {

        Pass
        {
            Tags{"LightMode"="ForwardBase" }

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"


            struct v2f
            {
                float4 pos : SV_POSITION;
                float3 normal : NORMAL;
                fixed3 color : COLOR;
                float4 wpos : TEXCOORD;
            };

            fixed4 _MainColor;
            fixed4 _HighLightColor;
            float _HighLightNum;

            fixed3 getLembertColor(in float3 wnormal)
            {
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                fixed3 color = _LightColor0.rgb * _MainColor.rgb * max(0,dot(wnormal,lightDir));
                return color;
            } 

            fixed3 getBlinnPhongColor(in float3 wpos, in float3 wnormal)
            {
                float3 viewDic = normalize(_WorldSpaceCameraPos.xyz - wpos);
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                float3 halfDir = normalize(viewDic + lightDir);
                fixed3 color = _LightColor0.rgb * _HighLightColor.rgb * pow(max(0,dot(wnormal,halfDir)),_HighLightNum);
                return color;
            }

            v2f vert (appdata_base v)
            {
                v2f data;
                data.pos = UnityObjectToClipPos(v.vertex);
                data.normal = UnityObjectToWorldNormal(v.normal);
                data.wpos = mul(unity_ObjectToWorld,v.vertex);
                return data;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                float3 wnormal = normalize(i.normal);
                fixed3 lambertColor = getLembertColor(wnormal);
                fixed3 blinnPhongColor = getBlinnPhongColor(i.wpos,wnormal);
                fixed3 color = UNITY_LIGHTMODEL_AMBIENT + lambertColor + blinnPhongColor;
                return fixed4(color,1);
            }
            ENDCG
        }
    }
}
```

