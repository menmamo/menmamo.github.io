---
title: "LineRenderer画线"
author: "mona"
date: 2024-06-17T11:46:00+08:00
categories: ["Unity"]
tags: ["Unity基础组件"]
---

## LineRenderer 画线

LineRenderer是Unity提供的一个用于画线的组件使用它我们可以在场景中绘制线段

一般可以用于

- 绘制攻击范围
- 武器红外线
- 辅助功能
- 其他画线功能

### LinRenderer组件属性

Loop 是否连接终点和起点

Positions线段的点

- Size 点的数量 两个点构成一个线段  xyz轴对应世界坐标
- width 可以修改线的粗细 通过曲线

Color 颜色

Corner Vertices 角顶点，圆角 增加此属性可以使线角看起来更圆

End Cap Vertices 终点和起点的圆角

Use World Space 是否使用世界坐标系

Materials 线使用的材质球

### 不常用属性

Alignment 对其方式

- View 视点 线段对这摄像机
- Transform Z 线段面向其Z轴

Texture Mode纹理模式

- Stretch拉伸 沿整条线映射纹理一次
- Tile 瓷砖平铺 不停的重复纹理
- Distribute Per Segment 分配执行
- Repeat Per Segment 重复显示

Shadow Bias 阴影偏移

Generate Lighting Data 生成光源数据

Lighting 光照影响

- Cast Shadows是否开启阴影
- Receive Shadows 接收阴影

Probes光照探针

- LightProbes光探测器模式
  - 不适用光探针
  - 使用内置光探针
  - 使用三维网络内插光探针
  - 自定义从材质决定

- Reflection Probes 反射探测器模式
  - 不使用反射探针
  - 启用混合反射探针
  - 启用混合反射探针 并且和天空盒融合
  - 启用普通探针 重叠式钚混合

### Additional Settings 附加设置

Motion Vectors运动矢量

- 使用相机运动来跟踪运动
- 使用特定对象来来跟踪运动
- 不跟踪

- Dynamic Occludee 动态遮挡剔除
- Sorting Layer排序图层
- Order in Layer此线段在图层中的顺序


### 可视化编辑

Simplify Preview 简化预览

Tolerance 宽容度 偏离值越大 偏差越大

编辑点模式

- Show Wireframe 显示线框
- Subdivide Selected 选择两个或者多个相邻的点 然后再两个点之间插入一个点

添加点模式

Input输入模式

- Mouse Position 鼠标位置
- Physics Raycast 基于物理射线
- Min VertexDistance 最小顶点距离 拖动鼠标创建点时 会在超出该距离时创建一个点
- Offset 偏移量

### LineRenderer代码相关

#### 通过代码添加一个线段

```c#
GameObject line = new GameObject();
line.name = "line";
var lineRenderer = line.AddComponent<LineRenderer>();
```

#### LineRenderer属性

```c#
lineRenderer.loop = true;//首尾相连
lineRenderer.startWidth = 0.02f;//开始宽
lineRenderer.endWidth = 0.02f;//结束宽
lineRenderer.startColor = Color.Red;//开始颜色
lineRenderer.endColor = Color.white;//结束颜色
lineRenderer.material = material;//设置材质
//设置点 要线设置点的数量
lineRenderer.positionCount = 4;
lineRenderer.SetPositions(vector3);//设置点的位置 传入Vector3数组
lineRenderer.generateLightingData = true;//使线段受光的影响
```