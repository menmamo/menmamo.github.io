---
title: "2D骨骼Spine"
author: "mona"
date: 2024-06-27T17:23:00+08:00
categories: ["Unity"]
tags: ["2D骨骼动画"]
---

## Spine是什么？

- Spine是一个收费的跨平台2D骨骼动画制作工具
- 他支持Unity、UE、Cocos2D、Cocos2D-x等等游戏引擎
- 相对Unity2018才退出的2D Animation Spine是目前商业游戏中较为常用的骨骼动画制作方案

##  导入 Spine

- 在Spine制作骨骼动画 并导出
- Spine导出的资源有三个文件
  - .json存储了骨骼信息
  - .png使用的图片图集
  - .atlas 图片在图集中的位置信息
- Unity中下载并导入Spine包
  - 下载地址：https://zh.esotericsoftware.com/spine-unity-download/
- 导入Spine导出的三个资源文件
- 当我们把这三个资源文件导入到已经引入了Spine运行库的Unity工程后会自动生成
  - \_Atlas 材质和.atlas.txt文件的引用配置文件
  - \_Material 材质文件
  - \_SkeletonData json和\_Atlas资源的引用配置文件

## 骨骼动画代码控制

### SkeletonData Asset参数

![](/usr/uploads/2024/06/image-20240627162126124.png)

**Skeleton Data骨骼数据**

- SkeletonData JSON 骨骼数据文件
- Scale 缩放大小
- Skeleton Data Modifiers 骨骼数据修改器
- Blend Mode Materizls 混合模式材质
- Apply Additive Materals 叠加材质
- Multiply Materials 相乘材质
- Screen Materials 屏幕材质

**Atlas Assets 图集资源**

 **Mix Setting 混合设置**

- Animation State Data 动画状态数据
- Default Mix Duration 默认混合持续时间
- Add Custom Mix 添加自定义混合（可以指定某两个动画之间的持续时间）

 **Preview 预览**

![](/usr/uploads/2024/06/image-20240627162305986.png)

- Animations 动画
- Setup Pose 设置姿势（还原到默认姿势）
- Create Anmiation Refrernce Assets 创建动画参考资源
- Slots 插槽相关（一个部位有多张图片构成，可以在这预览）
- SkeletonMecanim 骨骼机制
  - SkeletonMecanim 是SkeletonAnimation的替代品 他不是必须的
  - Controller 关联动画控制器

### SkeletonAnimaton组件参数

- SkeletonData Asset 关联的骨骼动画信息
- Animation Name 当前播放的动画名
- Loop 是否循环
- Initial Skin 初始蒙皮
- Time Scale 时间缩放 值越大 动画播放的越快
- Root Motion 是否添加跟运动的脚本 一般不添加

Advanced高级设置（基本上不会使用）

![](/usr/uploads/2024/06/image-20240627163334734.png)

- Initial Flip X/Y：初始翻转X和Y
- Update When Invisible：不可见时是否更新
  - Nothing：不更新
  - Only Animation Status：仅动画状态
  - Only Event Timelines：仅事件
  - Everything Except Mesh：除了网格其它都更新
  - Full Update：更新所有
- Use Single Submesh：使用单个子网格
- Fix Draw Order：固定提取顺序
- Immutable Triangles：不变三角形
- Clear State On Disable：禁用时清楚状态
- Separator Slot Names：分隔符插槽名称
- Z Spacing：Z间距
- Vertex Data：顶点数据
  - PMA Vertex Colors：PMA顶点颜色
  - Tint Black：淡黑色
  - Add Normals：添加法线
  - Solve Tangents：求解切线
- Add Skeleton Utility：添加骨骼公共程序

**Debug模式**（一般不会使用）

- Show Bone Names：显示骨骼名字
- Show Paths：显示路径
- Show Shapes：显示形状
- Show Constraints：显示约束条件
- ScaleX/Y：XY的缩放
- RGBA：颜色
- Bone：骨骼
  Show Bone Names：显示骨骼名字
- Slots：插槽
- Constraints：约束条件
- Draw Order and Separators：提取顺序和分离器相关
- Events：事件
- Data Counts：数据数量
  - Bones：骨骼数量
  - Slots：插槽数量
  - Skins：蒙皮数量
  - Events：事件数量
  - IK Constraints：IK约束数量
  - Transform Constraints：位置约束数量
  - Path Constraints：路径约束数量

## 代码控制动画播放

### 使用Animator控制动画

![](/usr/uploads/2024/06/image-20240627165335255.png)

把对象拖入场景时选择SkeletonMecanim 就可以使用Animator控制动画

### 使用SkeletonAnimaton控制动画

**获得SkeletonAnimaton组件**

```c#
SkeletonAnimation sa = GetComponent<SkeletonAnimation>();
```

**控制动画播放**

```c#
//方法一：直接改变SkeletonAnimation中参数
sa.loop = true;
sa.AnimationName = "jump";
//方法二：使用SkeletonAnimation中动画状态改变的函数
//马上播放
sa.AnimationState.SetAnimation(0, jumpName, false);
//排队播放
sa.AnimationState.AddAnimation(0, "walk", true, 0);
```

**转向**

```c#
sa.skeleton.ScaleX = -1;
```

**动画事件**

```c#
sa.AnimationState.Start += (t) =>
{
    print( sa.AnimationName +  "动画开始播放");
};
//动画被中断或者清除
sa.AnimationState.End += (t) =>
{
    print(sa.AnimationName + "动画中断或者清除");
};
//播放完成
sa.AnimationState.Complete += (t) =>
{
    print(sa.AnimationName + "动画播放完成");
};
//做动画时添加的自定义事件
sa.AnimationState.Event += (t, e) =>
{
    print(sa.AnimationName + "自定义事件");
};
```

**便捷特性**

可以在Inspector面板中 选择动画或者骨骼等名字 然后传入变量中

```c#
// 动画特性
[SpineAnimation]
public string animationName;
// 骨骼特性
[SpineBone]
public string boneName;
// 插槽特性
[SpineSlot]
public string slotName;
// 附件特性
[SpineAttachment]
public string attachmentName;
```

**获取骨骼、设置插槽附件**

```c#
//获取骨骼
Bone b = sa.skeleton.FindBone(boneName);
//选择插槽  设置附件内容 可以实现换装的效果
sa.skeleton.SetAttachment(slotName, attachmentName);
```

 **在UI中使用**

```
SkeletonGraphic(UnityUI);
```

