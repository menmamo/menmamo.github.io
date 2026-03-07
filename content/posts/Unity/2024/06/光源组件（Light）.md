---
title: "光源组件（Light）"
author: "mona"
date: 2024-06-17T11:35:00+08:00
categories: ["Unity"]
tags: []
---

## 光源

### 光源参数

**Type**光源类型

- Spot 聚光灯
  - Range：发光范围距离
  - Spot Angle 光锥角度
- Directional 方向光（环境光）
- Point点光源
- Area面光源
  - baked only 仅烘焙状态下有用

**Color** 颜色

**Mode**光源模式

- Realtime实时光源
  - 每一帧实时计算 性能消耗大
- Baked 烘培光源
  - 事先计算好 无法动态变化
- Mixed 混合光源
  - 预先计算+实时运算

**Intensity**光源亮度

**ShadowType** 阴影类型

- NoShadows 关闭阴影
- HardShadows 生硬阴影
- SoftShadows 柔和阴影

**Cookie** 投影遮罩

**Draw Halo** 球形光环开关

**Flare** 耀斑

**Culling Mast** 剔除遮罩层，决定那些层的对象收到该光源影响

**不常用：**

- Indirect Multiplier改变间接光的强度（反射） 低于1每次反弹会使光更暗 高于1更亮
- RealtimeShadows 阴影设置
  + Strength 阴影暗度 0-1 越大越黑
  + Resolution 阴影贴图渲染分辨率
  + Bias 阴影推离光源的距离
  + Normal Bias 阴影投射面沿法线收缩距离
  + NearPanel 渲染阴影的近裁切面
- Cookie Size 遮罩大小 只有方向光有
- Render Mode 渲染优先级
  + Auto 运行时确定
  + Important 以像素质量为单位进行渲染 效果逼真 消耗大
    Not Importand 以快速模式进行渲染

### 光面板设置

Window-Rendering-Lighting Settings

### Environment 环境相关设置

Skybox Material 可以改变天空盒

Sun Source 太阳来源  不设置会默认使用场景中最亮的方向光 代表太阳

### Environment Lighting 环境光设置

Source 环境光颜色

+ Skybox 天空和材质作为环境光颜色（默认）
+ Gradient 可以为天空、地平线、地图单独选择颜色和他们之间的混合

Intensity Myltiplier环境光亮度

Ambient Mode 全局光照模式（只有启用了实时全局和全局烘培时才有用）

+ Realtime 已弃用
+ Baked

### OtherSettings 其他设置

**Fog雾开关（不常用雾）**

Color雾颜色

Mode雾计算模式

- Linear 随距离线性增加
  + Start离摄像机多远开着有雾
  + End离摄像机多远完全遮挡
- Exponential随距离指数增加
  + Density 强度
- Exponential Qquart距离比指数更快的增加
  + Density 强度

Halo Texture 光源周围挥着光环的纹理

Halo Strength 光环可见性

Flare Fade Speed 耀斑淡出时间 最初出现之后淡出的时间

Flare Strength 耀斑可见性

Spot Cookie 聚光灯剪影纹理