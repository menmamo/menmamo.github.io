---
title: "2D骨骼动画（2D Animation）"
author: "mona"
date: 2024-06-24T14:36:00+08:00
categories: ["Unity"]
tags: ["2D骨骼动画"]
---

## 2D骨骼动画

2D骨骼动画主要方式有两种

- 使用Unity2018新加功能 2D Animation工具制作
- 使用跨平台骨骼动画制作工具 Spine制作

### 导入2D Animation工具

在Package Manager窗口 搜索2D Animation并安装

### 创建骨骼

![](/usr/uploads/2024/06/QQ截图20240624131548.png)



打开Sprite Editor  选择 Skinning Editor 

![](/usr/uploads/2024/06/image-20240624131816489.png)

#### Pose

- PreviewPose 预览模式，可以预览动作 并不会真正的改变变量
- RestPose 将骨骼和关节恢复到原始位置

#### Bone 骨骼相关

- Edit Bone 编辑骨骼 可以改变骨骼的位置 长度 方向 名称等
- Create Bone 创建骨骼
- Split Bone 拆分骨骼 将一个骨骼 一分为二

#### Geomtry蒙皮相关

蒙皮决定了骨骼主要控制哪一部分

- Auto Geometry自动蒙皮
- ![image-20240624133523939](/usr/uploads/2024/06/image-20240624133523939.png)
  - Outline Detail 边缘细节，值越大轮廓越细致
  - Alpha Tolerance 阿尔法公差值，控制蒙皮细节
  - Subdivide 细化程度，控制蒙皮细节
  - Weights 是否自动设置权重 一般勾选

- Edit Geometry 编辑蒙皮
- Create Vertex 创建顶点
- Create Edge 创建边线
- Split Edge 拆分边 用一个新的顶点分离一个边

#### Weight 权重相关

**Auto Weights自动赋予权重**

**Weight Slider编辑顶点和边的权重**

![](/usr/uploads/2024/06/image-20240624140519661.png)

- Mode：计算模式
  - Add And Subtract：加减法
  - Grow And Shrink：增长和收缩
  - Smooth：平滑
- Bone：设置权重的骨骼
- Normalize：标准化设置
- Amount：数量级
- Vertex Weight：顶点权重对应的骨骼

**Weight Brush用笔刷赋予权重**

![](/usr/uploads/2024/06/image-20240624140808612.png)

- Size：笔刷大小
- Hardness：笔刷强度，越大效果越明显
- Step：步数

**Bone Influence 选择骨骼用的Sprite**

![](/usr/uploads/2024/06/image-20240624140845004.png)

主要用于在psb图片中使用
骨骼控制点的图片关联

**Rig**

- Copy Rig 复制数据
- Paste Rig 粘贴数据

#### Visibility 面板

![](/usr/uploads/2024/06/image-20240624141113859.png)

- 左侧拖动条控制编辑时骨骼透明度
- 右侧拖动条控制编辑时权重透明度
- 下方列表浏览各骨骼名和骨骼层级信息

### 使用2D骨骼

- 添加Sprite Skin组件
- 点击CreateBones创建骨骼按钮
- 创建动画 修改骨骼的位置角度等等

### 为图集创建骨骼

1. 设置Sprite为图集模式
2. 对图集图片进行切片
3. 为图集中的每一个图 分别创建骨骼

### PSB文件创建骨骼

PSD和PSB两种格式，都是PS这款软件用于保存图像处理数据的文件格式

PSD和PSB两种格式并没有太大的区别，最大的区别是PSD格式兼容除PS以外的其它一些软件

而PSB只能用PS打开，在Unity中官方建议使用psb格式

使用PSB文件需要导入需要在Packages Manager窗口中引入 2D PSD Importer工具包

#### 设置PSB文件关键参数

- Extrude Edges 图片边缘延申网络
- Import Hidden 是否导入psb文件中的隐藏图层
- Moszic 启用后 将图层生成Sprite 并将他们合并成单个纹理
- Character Rig 是否实用人物已经绑定的骨骼
- Use Layer Grouping 使用psb文件中的图层分组
- Pivot 轴心点位置
- Reslice 从导入层重新生成Sprite 并清除对Sprite的任何更改，只有启用Moszic后又用
- Keep Duplicate Name 让Sprte名称保留psb文件中的名字

#### 为PSD绑定骨骼

- 需要对每一个图层的图绑定骨骼
- 在生成权重时 例如身体的骨骼变化会影响到手臂 可以通过 Bone Influnce中删除手臂

