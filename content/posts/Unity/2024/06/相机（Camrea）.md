---
title: "相机（Camrea）"
author: "mona"
date: 2024-06-17T11:43:00+08:00
categories: ["Unity"]
tags: ["相机"]
---

## Camera

### Camera 参数

- **Clear Flags**
  - skybox天空盒
  - Solid Color颜色填充
  - Depth only 只渲染该层，背景透明
  - Dont Clear 不移除，覆盖渲染
- **Culling Mask** 选择性渲染部分层级
  - 可以指定只渲染对应层级的对象（UI、Player）
- **Projection**透视方式
  - 透视模式
    - FOV  Axis 视口计算方式（横向和纵向）
    - Field of View 视口大小
    - Physical Camera（物理摄像机）勾选后模拟真实世界中的摄像机
      - Focal Length 焦距
      - Sensor Type 传感器类型
      - Sensor Size传感器尺寸
      - Lens Shift 透镜移位
      - Gate Fit 闸门配合
  - 正交模式（一般做2D游戏）
    - Size 摄影范围
- **Clipping Planes**（裁剪平面距离）
  - 摄像机只会渲染一定范围内的物体
  - 超出范围的物体会被裁剪
  - Near（最近）Far（最远）
- **Depth** 渲染顺序上的深度
  - 层级较高的摄像机会后被渲染 可以理解为是图层一样
  - 后被渲染的摄像机画面 会挡住先渲染的画面
- **Target Texture** 渲染纹理
  - 可以把摄像机画面 渲染到一张图上 可以用于制作小地图
  - 在Project右键创建Render Texture
- **Occlusion Cullin**g 是否启用剔除遮挡
  - 默认开启的 被遮挡的物体不渲染 可以节约一定的性能
- 不常用功能
  - Viewport Rect 视口范围
    - 屏幕上绘制该摄像机视图的位置和大小
      - 主要用于双摄像机游戏 0~1相当于宽高百分比
  - Allow HDR 是否允许高动态范围渲染
  - Allow MSAA 是否允许抗锯齿
  - Allow Dynamic Resolution 是否允许动态分辨率
  - Target Display 用于哪个显示器 主要用来开发多个屏幕的平台游戏
  - Target Eye 在哪个眼睛上渲染 VR游戏

### Camera代码相关

#### 获取摄像机

```c#
//得到主摄像机 摄像机的tag一定要是maincamera
Camera c = Camera.main;
//获取摄像机的数量
Camera.allCamerasCount;
//得到所有的摄像机
Camera[] allCamrea = Camera.allCameras;
```

#### 渲染相关委托

```c#
//摄像机剔除前处理的委托函数
//有一个参数camrea
Camera.onPreCull += (c)=>{

};
//摄像机 渲染前处理的委托
Camera.onPreRender +=(c)=>{
        
};
//摄像机 渲染后 处理的委托
Camera.onPostRender +=(c)=>{
        
};
```

#### 重要成员

界面上的参数 都可以在Camera中获取到

```c#
//得到主摄像机对象 上的深度 进行设置
Camrea.main.depth = 10;
//世界坐标转屏幕坐标（物体在这个摄像机上的屏幕坐标）
//xy对应屏幕坐标 z对应的是这个3d物体距离摄像机有多远
Vector3 v = Camera.main.WorldToScreenPoint(obj.transform.position);
//屏幕坐标转世界坐标
Vector3 mp = Input.Mouseposition;
//需要对z轴赋值 因为屏幕坐标是一个2D的
//如果Z为0 相当于是视口的交点
mp.z = 10;
Vector3 v = Camera.main.ScreenToWorld(mp);
```