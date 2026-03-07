---
title: "输入（Input）"
author: "mona"
date: 2024-06-17T11:39:00+08:00
categories: ["Unity"]
tags: ["Unity输入系统"]
---

## Unity输入

### 获取鼠标在屏幕上的位置

```c#
//返回的是屏幕坐标
Input.mousePosition
```

### 检测鼠标输入

```c#
//鼠标按下事件 0左键 1右键 2中建
Input.GetMouseButtonDown(0)
//鼠标抬起事件
Input.GetMouseButtonup(0)
//鼠标当前是否按下
Input.GetMouseButton(0)
//鼠标中键滚动 返回值y -1下 0  1上
Input.mouseScrollDelta
```

### 检测键盘输入

```c#
//键盘按下事件 KeyCode具体按键 也可以传入字符串"w"要小写
Input.GetKeyDown(KeyCode.W)
//键盘抬起
Input.GetKeyUp(KeyCode.W)
//键盘当前是否按下
Input.GetKey(KeyCode.W)
```

### Unity提供了一种方便控制位移的方法

- 使用Input.GetAxis方法 参数填InputManager中的字段
- Horizontal 左右方向  Vertical上下方向
- Mouse X 鼠标横向移动 Y 鼠标属相移动

```C#
//填入Project Settings中的InputManager
//这里面有很多unity自带的热键 也可以自定义
Input.GetAxis("Horizontal"); 返回-1 到1
Input.GetAxis("Vertical");

//和GetAxis使用方式相同 但只会返回-1 0 1 没有中间值
Input.GetAxisRaw("Horizontal");
```

### 一些其他方法

```c#
//是否有任意按或者鼠标长按
Input.anyKey
Input.anyKeyDown
//记录当前按下的按键
Input.InputString    
    
    
//手柄输入相关
//得到连接的手柄的所有按钮名字
string[] strs = Input.GetJoystickNames();
//某一个手柄按键按下
Input.GetButtonDown("Jump")
//移动设备触摸相关
if(Input.touchCount > 0)
{
    Touch t1 = Input.touches[0];
    //位置
    print(t1.position);
    //相对上次位置的变化
    print(t1.deltaPosition);
}

//是否启用多点触控
Input.multiTouchEnabled = false;
//陀螺仪（重力感应）
//是否开启陀螺仪 必须开启 才能正常使用
Input.gyro.enabled = true;
//重力加速度向量
print(Input.gyro.gravity);
//旋转速度
print(Input.gyro.rotationRate);
//陀螺仪 当前的旋转四元数 
//比如 用这个角度信息 来控制 场景上的一个3D物体受到重力影响
//手机怎么动 它怎么动
print(Input.gyro.attitude);
```

