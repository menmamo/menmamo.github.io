---
title: "TitleMap瓦片地图"
author: "mona"
date: 2024-06-24T11:04:00+08:00
categories: ["Unity"]
tags: ["2D地图"]
---

## 什么是Titlemap？

- Tilemap一般称之为 瓦片地图或者平铺地图
- 是Unity2017中新增的功能 主要用于快速编辑2D游戏中的场景

## 导入Titlemap包

Window-PackageManager 导入

## 瓦片

### 创建瓦片

方法一：

Assets——>Create——>Tile

方法二：

1. 打开Tile Palette瓦片调色板窗口
2. 新建一个瓦片地图编辑文件
3. 将资源拖入到窗口中选择要保存的路径

### 瓦片参数

- Preview ：预览图
- Sprite ：瓦片关联的精灵纹理
- Color ： 瓦片的颜色
- Collider Type : 碰撞器类型
  - None 不生成碰撞器
  - Sprite 基于精灵轮廓生成碰撞器的形状
  - Grid 基于瓦片单元格生成碰撞器形状

## 瓦片调色机

### 创建瓦片调色器参数

![](/usr/uploads/2024/06/image-20240621135043370.png)

- Name:瓦片调色器名称
- Grid：瓦片的网格布局
  - Rectangle：矩形瓦片
  - Hexagon：六边形瓦片
  - Isometric：等距瓦片，单元格为菱形
  - Isometric Z as Y：等距瓦片并且Unity将单元格Z轴转换为局部Y坐标
- Hexagon Type：六边形瓦片地图类型
  - Point Top：点朝顶部的六边形
  - Flat Top：面朝顶部的六边形
- Cell Size：瓦片绘制到单元格的大小
  - Automatic：基于瓦片资源的精灵大小自动设置
  - Manual：自定义大小

### 操作技巧

- 单击瓦片——选择瓦片
- 在多个瓦片上移动——选择多个瓦片
- Alt+左键拖动——平移
- 滚轮键拖动——平移
- 旋转滚轮键——放大/缩小

### 面板相关

![](/usr/uploads/2024/06/image-20240621135340593.png)

瓦片地图中有多层时，可以再这里选择层级进行绘制

![](/usr/uploads/2024/06/image-20240621135422749.png)

## 瓦片关键组件相关

### Grid组件参数

- Cell Size：网格单元格的大小
- Cell Gap：网格之间的间隔大小
- Cell Layout：网格单元的形状和排列
  - Rectangle：矩形
  - Hexagon：六边形
  - Isometric：等距布局，单元格为菱形
  - Isometric Z as Y：等距布局，但Unity将单元格Z轴转换为局部Y坐标
- Cell Swizzle：Unity将XYZ单元格坐标重新排序为你选择的类型

### Titlemap组件参数

- Animation Frame Rate：Unity播放瓦片动画的速率。相当于倍速
- Color：瓦片色调颜色
- Tile Anchor：瓦片的锚点偏移
- Orientation：瓦片地图上瓦片的方向。相当于2D平面使用的是Unity中的哪两个轴

### Titlemap Renderer组件

- Sort Order：设置所选瓦片地图上的瓦片排序方向
- Mode：渲染器的渲染模式
  - Chunk：按位置对瓦片进行分组，并将瓦片精灵一起批处理进行渲染，性能较好
  - Individual：单独渲染每个瓦片，会考虑他们的位置和排序顺序。会让瓦片精灵和场景中其它渲染器或自定义排序轴进行交互
- Detect Chunk Culling：渲染器如何剔除瓦片地图的边界
  - Auto：自动检测
  - Manual：手动设置拓展边界
- Chunk Culling Bounds：当选择手动设置剔除拓展边界时，可以在这里自己填写拓展的值

## 官方扩展包

### 新增瓦片类型

**规则瓦片Rule Tile(Hexagonal、Isometric)**

定义不同方向是否存在连接图片的规则

让我们更加快捷的进行地图编辑

![](/usr/uploads/2024/06/image-20240621140147204.png)

- Default Sprite：默认图片
- Default GameObject ：默认游戏对象
- Default Collider：默认碰撞器规则
- Tiling Rules：平铺规则 可以自己添加删除

**动画瓦片Animated Tile**

可以指定序列帧

产生可以播放序列帧动画的瓦片

![](/usr/uploads/2024/06/image-20240621140220511.png)

- Number Of Animated Sprites：动画有多少张图构成
- Minimum Speed：最小播放速度
- Maximum Speed：最大播放速度
- Start Time：开始播放的时间
- Strat Frame：从那一帧开始播放

**管道瓦片Pipeline Tile**

根据自己相邻瓦片的数量更换显示的图片

![](/usr/uploads/2024/06/image-20240621140242862.png)

- None：一个都不相邻时使用的图片
- One——Four：相邻1~4个时使用的图片

**随机瓦片Random Tile**

根据你设置的图片，随机从中选一个进行绘制

![](/usr/uploads/2024/06/image-20240621140309737.png)

- Number Of Sprites：随机图片数量（之后会随机取一个图片来使用）

**地形瓦片TerrainTile**

有点类似规则瓦片，只不过地形瓦片是帮助你定好的规则

![](/usr/uploads/2024/06/image-20240621140336983.png)

- Filled:填满
- Three Sides:三个面
- Two Sides and One Corner:两面一角
- Two Adjacent Sides:相邻两侧
- Two Opposite Sides:两个相对的侧面
- One Side and Two Corners:一侧和两个角
- One Side and One Upper Corner:一边和上角
- One Side:一面
- Four Corners:四个角
- Three Corners:三个角
- Two Adjacent Corners:两个相邻角
- Two Opposite Corners：两个相反的角
- One Corner：一个角
- Empty：空

**权重随机瓦片 WeightedRandomTile**

  可以不平均随机选择图片的瓦片

**知识点七 (高级)规则覆盖瓦片 (Advanced)Rule Override Tile**

  在规则瓦片的基础上 改变图片或者指定启用的规则

## 新建自定义笔刷

1. 预设体笔刷——用于快捷刷出想要创建的精灵
2. 预设体随机笔刷——用于快捷随机刷出想要创建的精灵
3. 随机笔刷——可以指定瓦片进行关联，随机刷出对应瓦片

## 扩展笔刷

1. Coordinate Brush 坐标笔刷 —— 可以实时看到格子坐标
2. GameObject Brush 游戏对象笔刷 —— 可以在场景中选择和擦除游戏对象仅限于选定的游戏对象的子级
3. Group Brush 组合笔刷 —— 可以设置参数 当点击一个瓦片样式时 会自动取出一个范围内的瓦片
4. Line Brush 线性笔刷 —— 决定起点和终点画一条线出来
5. Random Brush 随机笔刷 —— 和之前的自定义随机画笔类似，可以随机画出瓦片
6. Tint Brush 着色笔刷 —— 可以给瓦片着色 瓦片的颜色锁要开启（Inspector窗口切换Debug模式 修改Flags）
7. Tint Brush(Smooth) 光滑着色笔刷 —— 可以给瓦片进行渐变着色，需要按要求改变材质

## 代码控制

- Tilemap组件：用于管理瓦片地图
- TileBase组件：瓦片资源对象基类
- Grid组件：用于坐标转换

```c#
//瓦片地图信息 可以通过它得到瓦片格子
public Tilemap map;
//格子位置相关控制 可以通过它 进行坐标转换
public Grid grid;
//瓦片资源基类通过它可以得到瓦片资源
public TileBase tileBase;
```

**清空瓦片地图**

```c#
map.ClearAllTiles();
```

**获取指定坐标格子**

```c#
TileBase tmp = map.GetTile(Vector3Int.zero);
```

**设置删除瓦片**

```c#
map.SetTile(new Vector3Int(0, 2, 0), tileBase);
map.SetTile(new Vector3Int(1, 0, 0), null);
//删除多张 第一个参数填TileBase数组
map.SwapTile(tmp, tileBase);
```

**替换瓦片**

```c#
map.SwapTile(tmp, tileBase);
```

**世界坐标转格子坐标**

```c#
//参数传入世界坐标
grid.WorldToCell();
```

