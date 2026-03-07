---
title: "导航寻路系统(Navigation)"
author: "mona"
date: 2024-07-02T20:55:00+08:00
categories: ["Unity"]
tags: ["Unity基础组件"]
---

## 什么是导航寻路系统

- Unity中的导航寻路系统是能够让我们在游戏世界当中
- 让角色能够从一个起点准确的到达另一个终点
- 并且能够自动避开两个点之间的障碍物选择最近最合理的路径进行前往
- Unity中的导航寻路系统的本质
- 就是在A星寻路算法的基础上进行了拓展和优化

## 我们要学习那些内容

1. 导航网格(NavMesh)的生成——要想角色能够在场景中自动寻路产生行进路径，那么必须得先有场景地形数据，导航网格生成就是生成用于寻路的地形数据
2. 导航网格寻路组件(NavMesh Agent)——寻路组件就是帮助我们根据地形数据计算路径让角色动起来的关键
3. 导航网格连接组件(Off-Mesh Link)——当地形中间有断层，想让角色能从一个平面跳向另一个平面，网格连接组件时关键
4. 导航网格动态障碍物组件(NavMesh Obstacle)——地形中可能存在的可以移动或动态销毁的障碍物需要挂载的组件

## 使用导航寻路系统并生成导航网络

1. Window->AI->Navigation
2. 选择Object页签 选择地形物体后 将Navigation Static 选中
   - 选中Navigation Static实际上是将物体设置为了Static静态
   - ![](/usr/uploads/2024/07/image-20240702204322678.png)
3. 切换到bake页签 进行相关设置后 Bake生成

## Object 场景对象设置页签

![](/usr/uploads/2024/07/image-20240702204443795.png)

- **Sceme Filter**
  场景过滤器，配合Hierarchy窗口使用
  All：显示场景上所有对象
  MeshRenderers：显示挂载的网格渲染器的对象
  Terrains：显示挂载了地形脚本的对象
- **Navigation Static**
  导航静态物体开关
- **Generate OffMeshLinks**
  生成网格连接点开关
- **Navigation Area**
  导航区域选择，配合Areas页签使用

## Bake导航数据烘焙页签

![](/usr/uploads/2024/07/image-20240702204543184.png)

- **Agent Radius**
  代理半径
  决定了烘焙边缘精确度
  控制平台可行走区域和边缘可行走区域
- **Agent Height**
  代理高度
  决定了烘焙高度精确度
  控制拱桥是否可以穿越
- **Max Slope**
  最大坡度
  斜坡度数
  决定了斜坡是否可以行走
- **Step Height**
  最小楼梯高度
  决定了台阶是否可以行走
- **Generated off mesh Links**
  生成非网格连接
  两个分开的网格之间连接相关设置
  - Drop Height掉落高度 可以从这个高度掉下来
  - Jump Distance跳跃距离决定不同平面上的跳跃距离

**Advanced高级设置**

- **Manual Voxel Size**
  手动设置立体像素大小

  Voxel Size开启前者后可以在这里设置立体像素大小可以控制烘焙的准确度

  立体像素大小描述了生成的导航网格表示场景几何体的准确程度

  注意：立体像素大小减半会使内存使用量增加4倍，构建时间也增加4倍
  一般情况下不需要修改这个值
  除非想要极其准确的导航网格

- **Min Region Area**
  最小区域面积
  当面积小于此处值的导航网格区域会被移除

- **Height Mesh**
  高度网格构建开关

  主要用来解决楼梯烘焙为斜坡时
  希望在楼梯表面的位置准确的放置在楼梯平面上
  而不是斜坡上

  启用它会增加烘焙时间

## Areas导航地区页签

![](/usr/uploads/2024/07/image-20240702204910575.png)

一般配合配合Object页签使用

- **Name**
  区域名字
- **Cost**
  寻路消耗

## Agents代理页签用于配置寻路代理信息

![](/usr/uploads/2024/07/image-20240702205027113.png)

用于配置寻路代理信息 可以根据不同的角色或者对象 设置不同的 高度等等配置

## Uniy2022及以上版本的导航寻路系统

将导航寻路系统从Unity中剥离，需要在Package Manager窗口中安装AI Navigation

新版本中，不能单独设置物体的寻路静态属性

只需要直接将参与烘焙的物体设置为静态对象即可

##  导航网格寻路组件

- 通过创建 导航网络 我们已经准备好了地形相关数据
- 知道地形上哪些地方可以到达，哪些不能
- 那么寻路组件的作用就是帮助我们让角色可以在地形上准确的移动起来

### 寻路组件参数相关Nav Mesh Agent

![](/usr/uploads/2024/07/image-20240703133516704.png)

- **Agent Type**
  代理类型
  配合Agents页签使用
- **Base Offset**
  基础偏移值，相对对象轴心点的高度偏移
- **Steering**
  移动设置
- **Obstacle Avoidance**
  避障设置
  - Radius 半径，用于计算障碍物和其它寻路对象之间的碰撞
  - Height 高度，通过头顶障碍物时用于计算高度间隙使用
  - Quality 障碍躲避品质，越高躲避障碍越准确，但是性能消耗较大，如果不想主动避开其它动态障碍，可以设置为无，则只会解析碰撞
  - Priority 优先级，0~99， 避障时，数字较小的障碍物表示较高的优先级  优先级低的会忽略避障
- **Path Finding**
  路径寻找规则
  - Auto Traverse OffMesh Link
    是否开启自动遍历网格外的其它网格连接
    如果要自定义判断，则关闭此功能
  - Auto Repath
    是否开启自动重设路线
    如果开启，当到达路径后段时会再次尝试寻路，当没有到达目标的路径时，会生成一条到达与目标位置最近的可达点
  - Area Mask
    寻路时，考虑的区域
    如果寻路时不想考虑某些区域，则取消选中
    塔防游戏中常见的功能

### 寻路组件代码相关

**自动寻路设置目标点**

```c#
//设置目标点后 就会移动至目标点
agent.SetDestination(playerTranform.position);
```

**停止寻路**

```c#
agent.isStopped = true;
```

**不常用内容**

```c#
//1.面板参数相关 速度 加速度 旋转速度等等
print(agent.speed);
print(agent.acceleration);
print(agent.angularSpeed);
//2.其它重要属性
//2-1当前是否有路径
if( agent.hasPath )
{

}
//2-2代理目标点 可以设置 也可以得到
print(agent.destination);

//2-3是否停止 可以得到也可以设置
print(agent.isStopped);

//2-4当前路径
print(agent.path);

//2-5路径是否在计算中
if( agent.pathPending )
{

}
//2-6路径状态
print(agent.pathStatus);

//2-7是否更新位置
agent.updatePosition = true;

//2-8是否更新角度
agent.updateRotation = true;

//2-9代理速度
print(agent.velocity);
//手动寻路
//计算生成路径
NavMeshPath path = new NavMeshPath();
if( agent.CalculatePath(Vector3.zero, path) )
{

}
//设置新路径
if(agent.SetPath(path))
{

}
//清除路径
agent.ResetPath();
//调整到指定点位置
agent.Warp(Vector3.zero);
```

## 网格外连接组件Off Mesh Link

我们在烘焙地形数据的时候

可以生成网格外连接

但是它是满足条件的都会生成

而且是要在编辑模式下生成



如果我们只希望两个未连接的平面之间只有有限条连接路径可以跳跃过去

并且运行时可以动态添加

就可以使用网格外连接组件

达到“指哪打哪”的效果

###  网格外连接组件的使用

1. 使用两个对象作为两个平面之间的连接点（起点和终点）
2. 添加Off Mesh Link脚本进行关联
3. 设置参数

### 网格外连接组件参数

![](/usr/uploads/2024/07//image-20240703140322872.png)

- **Start**
  起始点
- **End**
  结束点
- **Cost Override**
  覆盖消耗值
  负数或0则使用所属Area区域中的消耗值
  如果为正数，则使用Area区域寻路消耗值*正数代表该连接点的寻路消耗
  相当于我们可以自定义连接点的寻路消耗
  主要用来解决当有"步行"和连接点都可以到达某一个目标时，希望优先选择步行区域，但是步行路径明显更远时修改该值
- **Bi Directional**
  是否开启双向连接点
  开启后可以从Start到End，反之也可以
  如果关闭，则只能Start到End
- **Activated**
  是否启用该连接点
  如果关闭，则自动寻路时相当于失效
- **Auto Update Positions**
  是否自动更新位置
  如果启用
  当开始和结束位置改变时，导航网格也将更新
  如果不启用，即使改变了开始结束位置，也会按照刚开始的位置进行计算

## 导航网格动态障碍组件

在游戏中常常会有这样的一个功能

场景中有一道门，如果这道门没有被破坏是不能自动导航到门后场景的

只有当这道门被破坏了，才可以通过此处前往下一场景

而类似这样的物体本身是不需要进行寻路的所以没有必要为它添加NavMeshAgent脚本

这时就会使用动态障碍组件实现该功能

### 导航动态障碍物组件的使用

1. 为需要进行动态阻挡的对象添加NavMeshObstacle组件
2. 设置相关参数
3. 代码逻辑控制其的移动或者显隐

### 导航动态障碍物组件参数

![](/usr/uploads/2024/07/image-20240703141925977.png)

- **Shape**

  动态障碍的形状

  - Capsule：胶囊
  - Box 盒子

- **Carve**

  是否开启雕刻功能

  开启后，障碍物会在网格网络中挖一个孔，就是生成对应的网络信息 使其无法前往

  如果动态障碍物是固定不动的建议开启 比如门 木桶 等等

  如果是频繁移动的障碍物 则不建议开启

  - **Move Threshold**

    移动阈值

    当障碍物移动超过该距离时 会认为障碍物在移动状态 会更新移动的孔

  - **Time To Stationary**

    障碍物作为精致状态 需要等待的时间 单位为秒

    当精致时间超过该值 则会认为障碍物是静止的

  - **Carve Only Stationary**

    只有静止状态时才会计算孔