---
title: "Phong高光反射模型"
author: "mona"
date: 2025-07-28T22:52:00+08:00
categories: ["Unity"]
tags: ["shader"]
---

## Phone式高光反射模型的来历和原理

高光反射模型 没有单一的发明者 因为有多种计算的方式

和半兰伯特光照模型一样，是经过很多从业者的研究和发展演化而来

其中比较关键的贡献者为：

**Phong光照模型的提出者：**

裴祥风（Bui-Tuong Phong，越南裔美国计算机学家）

**Blinn-Phone光照模型的提出者：**

吉姆·布林（Jim Blinn，美国计算机科学家）

**原理:**

基于光的反射行为和观察者的位置决定高光反射的表现效果

并且通过对余弦值取n次幂表示光泽度（或反光度）

------

## Phong式高光反射光照模型的公式

公式：

高光反射光照颜色 = 光源颜色 * 材质高光反射颜色 * max(0,标准化后观察方向向量 · 标准化后的反射方向)<sup>幂</sup>

- 标准化后观察方向向量· 标准化后的反射方向 得到的结果就是 cosθ
- 幂 代表的是光泽度  余弦值取n次幂

-------

## 在Shader中获取公式中的关键信息

| 信息                         | 语法                                      |
| ---------------------------- | ----------------------------------------- |
| 观察者的位置（摄像机的位置） | _WorldSpaceCameraPos                      |
| 相对于法向量的反射向量 方法  | reflect(入射向量,顶点法向量) 返回反射向量 |
| 指数幂 方法                  | pow(底数，指数) 返回计算结果              |

------

## Phong式高光反射模型Shader实现

### 顶点着色器

```
Shader "Unlit/Phong"
{
    Properties
    {
        //高光颜色
        _SpeColor("_SpecColor",color) = (1,1,1,1)
        //光泽度
        _SpecNum("_SpecNum",Range(0,20)) = 0.5
    }
    SubShader
    {
        Tags{"LightMode"="ForwardBase"}

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            struct v2f
            {
                //裁剪空间下的顶点坐标
                float4 pos:SV_POSITION;
                //颜色信息
                fixed3 color:COLOR;
            };
            fixed4 _SpeColor;
            float _SpecNum;
            v2f vert (appdata_base v)
            {
                v2f data;
                data.pos = UnityObjectToClipPos(v.vertex);
                //世界坐标系下的顶点坐标
                float4 wordPos = mul(UNITY_MATRIX_M , v.vertex);
                //视角向量
                float3 viewDir = normalize(_WorldSpaceCameraPos - wordPos.xyz);
                //反射向量
                float3 worldNormal = UnityObjectToWorldNormal(v.normal);//世界坐标系下的法向量
                float normalizeLightPos = normalize(_WorldSpaceLightPos0.xyz);//世界坐标系下的灯光向量
                float3 specDir = reflect(-normalizeLightPos , worldNormal);
                data.color = _LightColor0.rgb * _SpeColor.rgb * pow(max(0,dot(viewDir,specDir)), _SpecNum);
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

### 片元着色器

```
Shader "Unlit/PhongFarg"
{
    Properties
    {
        //高光颜色
        _SpeColor("_SpecColor",color) = (1,1,1,1)
        //光泽度
        _SpecNum("_SpecNum",Range(0,20)) = 0.5

    }
    SubShader
    {
        Tags {"LightMode"="ForwardBase"}
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            struct v2f
            {
                float4 pos:SV_POSITION;
                float3 normal:NORMAL;
                float3 wpos:TEXCOORD0; //存世界坐标下的顶点坐标;
            };

            v2f vert (appdata_base v)
            {
                v2f data;
                data.pos = UnityObjectToClipPos(v.vertex);
                data.normal = UnityObjectToWorldNormal(v.normal);
                //data.wpos = mul(UNITY_MATRIX_M,v.vertex);
                data.wpos = mul(unity_ObjectToWorld,v.vertex);
                return data;
            }

            fixed4 _SpeColor;
            float _SpecNum;
            fixed4 frag (v2f i) : SV_Target
            {
                //计算视角向量
                float3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.wpos);
                //计算反射向量
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                float3 reflectDir = reflect(-lightDir, i.normal);
                //计算高光反射颜色
                fixed3 specColor = _LightColor0.xyz * _SpeColor.xyz * pow(max(0, dot(viewDir, reflectDir)), _SpecNum);
                return fixed4(specColor, 1);
            }
            ENDCG
        }
    }
}

```

