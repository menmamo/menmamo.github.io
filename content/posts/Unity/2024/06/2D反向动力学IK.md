---
title: "2D反向动力学IK"
author: "mona"
date: 2024-06-24T17:46:00+08:00
categories: ["Unity"]
tags: ["2D骨骼动画"]
---

- 在骨骼动画中，构建骨骼的方法被称为正向动力学
  - 子骨骼（关节）的位置根据父骨骼（关节）的旋转而改变
- 而IK全称是Inverse Kinematics，翻译过来的意思就是反向动力学的意思
- 它和正向动力学恰恰相反
- 它的表现形式是，子骨骼（关节）末端的位置改变会带动自己以及自己的父骨骼（关节）旋转

### 导入IK包

- 在Package Manager窗口中引入2D IK工具包
- 需要在Advanced高级选项中选中Show preview packages（显示预览包）
- 这样才能看到2D IK相关内容

### 使用IK

- 需要在对象添加IK Manager 2D组件
- 在关节末端 创建空物体（ik点位置）
- 在IK Manager 2D中的IK Solvers里面添加ik
- 添加ik后Unity会自动创建ik对象
- 在ik对象中 将骨骼节点和Effector相关联
- 创建Target

### IK Manager 2D参数

- IK Solvers：IK解算器，在这里添加IK
  - Chain(CCD) 可以自定义影响N个关节点，不能反向
  - Chain（FABRIK）可以自定义影响N个关节点，可以反向
  - Limb 只会影响3个关节点
- Weight权重 当有多个IK控制同一点时权重会影响控制的百分比
- Restore Default Pose 恢复默认位置

### Limb Solver 2D组件参数

- Effector：默认的ik点位置
- Target 会根据ik点位置生成真正的ik对象
- Flip 反向
- Constrain Rotation 约束旋转程度
- Solve from Default Pose 从默认姿势进行求解计算
- Weight 权重

### CCD  Solver 2D组件参数

- Chain Length：IK影响的骨骼数量
- Iterations：算法运行的次数
- Tolerance：容错程度
- Volcoity 速度
- Constrain Rotation 约束旋转程度
- Solve from Default Pose 从默认姿势进行求解计算
- Weight 权重

