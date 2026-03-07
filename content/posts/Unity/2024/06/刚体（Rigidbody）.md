---
title: "刚体（Rigidbody）"
author: "mona"
date: 2024-06-17T11:35:00+08:00
categories: ["Unity"]
tags: []
---

### 碰撞产生的条件

两个物体都要有碰撞器 至少有一个物体有刚体组件

### 刚体组件

#### 面板参数

- Mass 质量：默认单位为千克 质量越大惯性越大
- Drag空气阻力：根据力移动对象时影响对象的空去阻力大小 0代表没有阻力
- Angular Drag根据扭矩旋转对象时影响对象的空气阻力大小 0代表没有阻力
- Use Gravity 是否受到重力影响
- Is Kinematic 如果启用此选项，则对象将不会呗物理引擎驱动，只能通过（Transform）对其进行操作，对于移动平台，或者如果要动画化附加了HingeJoint的刚体，此属性将非常有用
- InterPolate插值运算 让刚体移动更平滑 （物理系统是在Fixed Update中计算的，如果Fixed Update的更新频率很低 ，可以使用插值运算来平滑移动 一般使用前一帧）
  - None 不应用差值计算
  - Interpolate根据前一帧来平滑移动
  - Extrapolate根据下一帧来平滑移动
- Collision Detection碰撞检测模式：可用于解决快速移动的对象穿过其他对象而不触发碰撞的问题
  - Discrete离散检测 ：对场景中所有其他碰撞体使用离散碰撞检测 默认值
  - Continuous连续检测 ：对于刚体 使用离散检测，对于没有刚体的物体 使用连续碰撞检测
  - Continuous Dynamic连续动态检测 ：对设置为连续和连续动态的刚体使用 连续检测 对于没有刚体的物体使用连续碰撞检测
  - Continuous  Speculutive 连续推测检测：对刚体和碰撞体使用推测性连续碰撞检测，该方法通常笔连续动态碰撞检测成本更低
- 性能消耗关系：动态连续检测>连续推断检测>连续检测>离散检测
- ![](/usr/uploads/2025/01/920065697.png)
- Constraints约束 对刚体运动的限制
  - FreezePosition 有选择的停止刚体沿世界XYZ轴移动
  - FreezeRotaition有选择的停止刚体沿世界XYZ轴旋转

### 刚体加力

#### 获取刚体组件

```c#
//获取刚体
Rigidbody rigidBody;
rigidBody = GetComponent<Rigidbody>();
```

#### 添加力

```c#
//世界坐标系 Z轴正方向添加了一个力
//加力过后 对象是否停止移动 是由阻力决定的
rigidBody.AddForce(Vector3.forward * 10);
//本地坐标系
rigidBody.AddRelativeForce(Vector3.forward *10);
```

#### 添加扭矩力

```c#
//世界坐标
rigidBody.AddTorque(Vector3.up * 10);
//本地坐标
rigidBody.AddRelativeTorque(Vector3.up * 10);
```

#### 直接修改速度

```c#
rigidBody.velocity = Vector3.forward * 5;
```

#### 模拟爆炸效果

```c#
//第一个参数力的大小
//第二个参数 爆炸的中心
//第三个参数 爆炸的半径
//只影响调用了这个方法的对象
rigidBody.AddExplosionForce(100,Vector3.Zero,20);
```

#### 力的模式

- 主要的作用 主要就是 计算的方式不同
- 由于计算方式不同 最终的移动速度也会不同
- ForceMode.Acceleration 给物体增加一个持续的力 忽略其质量
- ForceMode.Force 给物体增加一个持续的力 与物体的质量有关
- ForceMode.Impulse 给物体增加一个瞬间的力 与物体的质量有关 忽略时间默认1
- ForceMode.VelocityChange 给物体一个瞬间的力 忽略质量

```c#
rigidBody.AddForce(Vector3.forward * 10,ForceMode.Force);
```

### 力场脚本

- 可在Inspector面板中添加Constant Force组件
- 添加Constant Force组件时会自动添加一个刚体组件
- 可以通过面板上的参数 给这个对象一个持续的力

### 刚体的休眠

在某些情况下 添加了刚体的物体 会不参与物理计算，这是因为Unity为了节约性能 给刚体增加了一个休眠机制 可以通过脚本来判断是否为休眠状态

```c#
if(rigidBody.IsSleeping())rigidBody.WakeUp();
```