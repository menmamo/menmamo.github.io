---
title: "Phong光照模型"
author: "mona"
date: 2025-08-09T22:18:00+08:00
categories: ["Unity"]
tags: ["shader"]
---

## 两个颜色相加

我们在编写兰伯特光照模型时了解了 两个颜色相乘

他的作用就是让两个颜色叠加在一起

也就是让两个颜色的RGBA值各自相乘 得到一个新的颜色

两个颜色相加 同样是让两个颜色叠加在一起

区别是：

**相乘**

颜色相乘时，最终颜色会往黑色靠拢

计算两个颜色混合时一般用颜色相乘，因为真实世界中多个颜色混在一起最终会变成黑色。

**相加**

颜色相加时，最终颜色会往白色靠拢

计算光照反射时一般用颜色相加，因为向白色靠拢能带来 更亮的感觉，复合光的表现

------

## Unity Shader中的环境光

我们在编写兰伯特光照模型时

在计算完漫反射光照后，加上了一个环境光变量 `UNITY_LIGHTMODEL_AMBIENT`

这个环境光变量其实是可以在Unity中进行设置的

Window——>Rendering——>Lighting

Environment(环境)页签中的Environment Lighting(环境光)这里可以设置环境光来源

当是Skybox和Color时，我们可以通过`UNITY_LIGHTMODEL_AMBIENT` 获取到对应环境光颜色

当是Gradient（渐变）时，通过以下3个成员可以得到对应的环境光

`unity_AmbientSky`（周围的天空环境光）

`unity_AmbientEquator`（周围的赤道环境光）

`unity_AmbientGround`（周围的地面环境光）

注意：

这些内置变量都包含在 `UnityShaderVariables.cginc` 中

在编译时，会自动包含该文件，可以不用手动包含

------

## Phong光照模型的来历和原理

它是由裴祥风（Bui-Tuong Phong，越南裔美国计算机学家）在1975年时，提出的一种局部光照经验模型

裴祥风认为物体表面反射光线是由三部分组成的

环境光 + 漫反射光 + 镜面反射光（高光反射光）

是一种经验模型

------

## Phong光照模型的公式

物体表面光照颜色 = 环境光颜色 + 漫反射光颜色 + 高光反射光颜色

其中：

环境光颜色 = UNITY_LIGHTMODEL_AMBIENT(unity_AmbientSky、unity_AmbientEquator、unity_AmbientGround)

漫反射光颜色 = 兰伯特光照模型 计算得到的颜色

高光反射光颜色 = Phong式高光反射光照模型 计算得到的颜色

------

## 逐顶点实现

```
Shader "Unlit/Phong"
{
    Properties
    {
        //漫反射颜色
       _MainColor("MainColor", COLOR) = (1,1,1,1)
       //高光颜色
       _HighLightColor("HighLightColor",COLOR) = (1,1,1,1)
       //光泽度
       _HighLightNum("HighLightNum",Range(0,20)) = 0.5
    }
    SubShader
    {
        Pass
        {
            //光照模式 向前渲染
            Tags { "LightMode"="ForwardBase" }

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            struct v2f
            {
                float4 pos:SV_POSITION;
                fixed3 color:COLOR;
            };

            fixed4 _MainColor; //漫反射颜色
            fixed4 _HighLightColor; //高光颜色
            float _HighLightNum; //光泽度

            
            fixed3 LambertHandle(in float3 normal)//世界坐标系下的归一化法向量
            {
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                fixed3 color = _LightColor0.rgb * _MainColor.rgb * max(0, dot(normal, lightDir));
                //fixed3 color = _LightColor0.rgb * _MainColor.rgb * (0.5 * dot(normal, lightDir) + 0.5);//半兰伯特
                return color;
            }

            fixed3 PhongHandle(in float3 normal, in float3 vertex)//世界坐标系下的归一化法向量和归一化顶点坐标
            {
                //反射向量
                float3 reflectDir = reflect(-normalize(_WorldSpaceLightPos0.xyz),normal);
                //视角向量  归一化（世界坐标系下摄像机位置-世界坐标系下顶点坐标）
                float3 viewDir = normalize(_WorldSpaceCameraPos.xyz - vertex);
                fixed3 color = _LightColor0.rgb * _HighLightColor * pow(max(0,dot(viewDir,reflectDir)),_HighLightNum);
                return color;
            }

            //顶点着色器
            v2f vert (appdata_base v)
            {
                v2f data;
                data.pos = UnityObjectToClipPos(v.vertex); //将 模型坐标系下的顶点坐标 转换到其次裁剪坐标系下

                //将法向量 从模型空间转到世界空间 并归一化
                float3 worldNormalPos = normalize(UnityObjectToWorldNormal(v.normal).xyz);

                //计算兰伯特光照
                fixed3 lamberColor = LambertHandle(worldNormalPos);
                
                //计算Phong高光反射
                float3 worldVertexPos = normalize(mul(UNITY_MATRIX_M, v.vertex).xyz); //世界坐标系下的顶点坐标(模型先顶点坐标× MVP矩阵中的M)
                fixed3 phongColor = PhongHandle(worldNormalPos,worldVertexPos);
                
                //将颜色相加
                data.color = UNITY_LIGHTMODEL_AMBIENT.rgb + lamberColor + phongColor;
                return data;
            }

            //片元着色器
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

## 逐片元实现

```
Shader "Unlit/PhongF"
{
    Properties
    {
        //漫反射颜色
       _MainColor("MainColor", COLOR) = (1,1,1,1)
       //高光颜色
       _HighLightColor("HighLightColor",COLOR) = (1,1,1,1)
       //光泽度
       _HighLightNum("HighLightNum",Range(0,20)) = 0.5
    }
    SubShader
    {

        Pass
        {
            //光照模式 向前渲染
            Tags { "LightMode"="ForwardBase" }

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "Lighting.cginc"

            struct v2f
            {
                float4 pos:SV_POSITION;
                float3 wNormal:NORMAL;
                fixed3 color:COLOR;
                float4 wPos:TEXCOORD0;
            };

            fixed4 _MainColor; //漫反射颜色
            fixed4 _HighLightColor; //高光颜色
            float _HighLightNum; //光泽度

            
            fixed3 LambertHandle(in float3 normal)//世界坐标系下的归一化法向量
            {
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                fixed3 color = _LightColor0.rgb * _MainColor.rgb * max(0, dot(normal, lightDir));
                return color;
            }

            fixed3 PhongHandle(in float3 normal, in float3 vertex)//世界坐标系下的归一化法向量和归一化顶点坐标
            {
                //反射向量
                float3 reflectDir = reflect(-normalize(_WorldSpaceLightPos0.xyz),normal);
                //视角向量  归一化（世界坐标系下摄像机位置-世界坐标系下顶点坐标）
                float3 viewDir = normalize(_WorldSpaceCameraPos.xyz - vertex);
                fixed3 color = _LightColor0.rgb * _HighLightColor * pow(max(0,dot(viewDir,reflectDir)),_HighLightNum);
                return color;
            }

            v2f vert (appdata_base v)
            {
                v2f data;
                data.pos = UnityObjectToClipPos(v.vertex);
                data.wNormal = UnityObjectToWorldNormal(v.normal);
                data.wPos = mul(unity_ObjectToWorld,v.vertex);
                return data;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                float3 normal = normalize(i.wNormal).xyz;
                float3 vertex = normalize(i.wPos).xyz;
                fixed3 lambertColor = LambertHandle(normal);
                fixed3 phongColor = PhongHandle(normal,vertex);
                fixed3 color = UNITY_LIGHTMODEL_AMBIENT + lambertColor + phongColor;
                return fixed4(color,1);
            }
            ENDCG
        }
    }
}
```

