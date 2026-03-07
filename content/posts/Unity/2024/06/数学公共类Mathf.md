---
title: "数学公共类Mathf"
author: "mona"
date: 2024-06-17T11:41:00+08:00
categories: ["Unity"]
tags: ["UnityEngine"]
---

## Mathf公共类

- Math是C#中封装好的用于计算的工具类 位于System命名空间中
- Mathf是Unity中封装好的 用于数学计算的工具结构体 位于UnityEngine命名空间中
- 在Unity开发中 一般使用Mathf

### Mathf中的常用方法

#### Π

```c#
Mathf.PI
```

#### 取绝对值 abs

```c#
Mathf.Abs(-5);
```

#### 向上取整

```c#
Mathf.CeilToInt(1.3f);//2
```

#### 向下取整

```c#
Mathf.FloorToInt(1.8f);//1
```

#### 钳制函数 Clamp

```c#
Mathf.Clamp(value,min,max);
//比最小值小 返回最小值 比最大值大 返回最大值 
```

#### 最大值

```c#
Mathf.Max(1,2,3); //3
```

#### 最小值

```c#
Mathf.Min(1,2,3);//1
```

#### 一个数的n次幂

```c#
Mathf.Pow(3,2);//9
```

#### 四舍五入

```c#
Mathf.RoundToInt(1.4f);//1
Mathf.RoundToInt(1.5f);//2
```

#### 返回一个数的平方根 Sqrt

```c#
Mathf.Sqrt(4);//2
```

#### 判断一个数是2的几次幂

```c#
Mathf.IsPowerOfTwo(256);//true
```

#### 判断正负数

```c#
Mathf.Sign(-1);//正数返回1 负数返回-1
```

#### 插值计算

```
Mathf.Lerp(0,10,0.5f); //0.5
```

#### 弧度角度转换

```c#
//Unity默认使用弧度作为单位
//弧度转角度
float rad = 1;
float anger = rad * Mathf.Rad2Deg;
//角度转弧度
anger = 1;
rad = anger * Mathf.Deg2Rad;
```

### 三角函数

- 正弦函数（sin）

  - sinβ = 角的对边/斜边
  - sinβ = A/C

- 余弦函数（cos）

  - cosβ = 角的临边/斜边
  - cosβ = B/C

- 正切函数（tan）

  - tan = 对边/临边
  - tan =  c / b 


#### 计算三角函数

```c#
//注意：Mathf中的三角函数相关函数，传入的参数需要时弧度值
print(Mathf.Sin(30 * Mathf.Deg2Rad));//0.5
print(Mathf.Cos(60 * Mathf.Deg2Rad));//0.5
```

#### 反三角函数（通过角度来得到正弦或者余弦值）

```c#
rad = Mathf.Asin(0.5f);
print(rad * Mathf.Rad2Deg);
rad = Mathf.Acos(0.5f);
print(rad * Mathf.Rad2Deg);
```