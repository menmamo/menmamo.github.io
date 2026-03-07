---
title: "新输入系统Input System"
author: "mona"
date: 2024-07-30T16:40:00+08:00
categories: ["Unity"]
tags: ["Unity输入系统"]
---

## InputSystem是什么

InputSystem是Unity提供的一种新的输入系统

它相对于老的输入系统 扩展性和可自定义性更好

需要Unity2019.4以上版本 .Net 4 runtime

## 新老输入系统的区别

老输入系统需要我们自己写各种检测代码来判断输入并处理逻辑

新输入系统 不仅可以像老输入系统一样使用还添加了配置输入的概念

新输入系统将输入操作进行封装，让我们可以在Unity内进行输入配置文件编辑

我们不需要写代码来判断设备输入，只需要把工作重心放在逻辑处理上

## 导入InputSystem

**InputSystem包导入**

packageManager中导入Input System

**选择开启哪一种输入模式**

使用InputSystem默认会关闭老输入系统

选择InputSystem和 老InputManager的启用情况

File——>Build Setting——>Player Setting——>Other——>Active Input Handling

可以同时启用也可以只启用其中之一,每次启用后会重启Unity

## 代码控制 输入（不常用）

### 设备类

新输入系统 提供了对应的输入设备类 帮助我们对某一种设备进行输入检测

```c#
//键盘
Keyboard keyBoard = Keyboard.current;
//鼠标
Mouse mouse = Mouse.current;
//触摸屏
Touchscreen ts = Touchscreen.current;
//手柄
Gamepad gamePad = Gamepad.current;
```

### 检测键盘输入

```c#
//按下
if(Keyboard.current.enterKey.wasPressedThisFrame)
{
    Debug.log("检测当前帧 是否有 按下 操作");
}
//抬起
if(Keyboard.current.dKey.wasReleasedThisFrame)
{

}
//长按
if(Keyboard.current.spaceKey.isPressed)
{

}
//通过事件监听按键按下
keyBoard.onTextInput += (c) =>
{
    print("通过lambda表达式" + c);
};
```

### 检测鼠标输入

**鼠标按键**

```c#
//鼠标左键
Mouse.current.leftButton
//鼠标右键
Mouse.current.rightButton
//鼠标中键
Mouse.current.middleButton
//鼠标滚轮 前
Mouse.current.forwardButton
//鼠标滚轮 后
Mouse.current.backButton
```

**鼠标事件**

```c#
//鼠标按下
Mouse.current.leftButton.wasPressedThisFrame
//鼠标抬起
Mouse.current.leftButton.wasReleasedThisFrame
//鼠标长按
Mouse.current.leftButton.isPressed
```

**鼠标位置相关**

```c#
//获取当前鼠标位置
mouse.position.ReadValue();
//得到鼠标两帧之间的一个偏移向量 
mouse.delta.ReadValue();
//鼠标中间 滚轮的方向向量
mouse.scroll.ReadValue();
```

### 检测触屏输入

**得到触摸屏设备**

```c#
Touchscreen ts = Touchscreen.current;
//由于触屏相关都是在移动平台或提供触屏的设备上使用
//所以在使用时最好做一次判空
if (ts == null)
    return;
```

**得到触屏手指信息**

```c#
//得到触屏手指数量
print(ts.touches.Count);
//得到单个触屏手指
ts.touches[1];
//得到所有触屏手指
foreach (var item in ts.touches)
{

}
```

**手指按下 抬起 长按 点击**

```c#
//获取指定索引手指
TouchControl tc = ts.touches[0];
//按下 抬起
if(tc.press.wasPressedThisFrame)
{

}
if(tc.press.wasReleasedThisFrame)
{

}
//长按
if(tc.press.isPressed)
{

}

//点击手势
if(tc.tap.isPressed)
{

}

//连续点击次数
print(tc.tapCount);
```

**手指位置等相关信息**

```c#
//位置
print(tc.position.ReadValue());
//第一次接触时位置
print(tc.startPosition.ReadValue());
//接触区域大小
tc.radius.ReadValue();
//偏移位置
tc.delta.ReadValue();
```

**得到当前手指的 状态**

```c#
UnityEngine.InputSystem.TouchPhase tp = tc.phase.ReadValue();
switch (tp)
{
    //无
    case UnityEngine.InputSystem.TouchPhase.None:
        break;
    //开始接触
    case UnityEngine.InputSystem.TouchPhase.Began:
        break;
    //移动
    case UnityEngine.InputSystem.TouchPhase.Moved:
        break;
    //结束
    case UnityEngine.InputSystem.TouchPhase.Ended:
        break;
    //取消
    case UnityEngine.InputSystem.TouchPhase.Canceled:
        break;
    //静止
    case UnityEngine.InputSystem.TouchPhase.Stationary:
        break;
    default:
        break;
}
```

### 检测手柄输入

**获取手柄设备**

```c#
Gamepad gamePad = Gamepad.current;
if (gamePad == null)
    return;
```

**获取手柄摇杆方向**

```c#
//摇杆方向
//左摇杆
print(gamePad.leftStick.ReadValue());
//右摇杆
print(gamePad.rightStick.ReadValue());
```

**摇杆按下**

```c#
if(gamePad.leftStickButton.wasPressedThisFrame)
{

}
if(gamePad.leftStickButton.wasReleasedThisFrame)
{

}
if(gamePad.leftStickButton.isPressed)
{

}
```

**手柄方向键**

```c#
//对应手柄上4个方向键 上下左右
//gamePad.dpad.left
//gamePad.dpad.right
//gamePad.dpad.up
//gamePad.dpad.down
if(gamePad.dpad.left.wasPressedThisFrame)
{

}
if(gamePad.dpad.left.wasReleasedThisFrame)
{

}
if(gamePad.dpad.left.isPressed)
{

}
```

**手柄右侧按键**

```c#
//通用的检测方式 东南西北
//Y、△
gamePad.buttonNorth
//A、X
gamePad.buttonSouth
//X、□
gamePad.buttonWest
//B、○
gamePad.buttonEast
//PS手柄 检测 ○ x △ □
//○
gamePad.circleButton
//△
gamePad.triangleButton
//□
gamePad.squareButton
//X
gamePad.crossButton
// xbox Nintendo 手柄 ABXY
gamePad.xButton
gamePad.aButton
gamePad.bButton
gamePad.yButton
```

 **手柄中央按键**

```c#
gamePad.startButton
gamePad.selectButton
```

**手柄肩键**

```c#
//左上右上 肩部键位
//左右前方肩部键
gamePad.leftShoulder
gamePad.rightShoulder

//左右后方触发键
gamePad.leftTrigger
gamePad.rightTrigger
```

### 其他输入

- Joystick—摇杆
- Pen—电子笔
- Sensor（传感器）
- Gyroscope—陀螺仪
- GravitySensor—重力传感器
- 加速传感器、光照传感器等等

## InputAction类

### Action Type动作类型

- **Value：值类型**

  主要用于状态连续更改的输入，例如鼠标的移动，手柄的遥感。如果有多个设备绑定这个Action，只会发送其中一个设备（最受控制的）的输入

- **Button：按钮类型**

  用于每次按下时触发的Action

- **Pass Through：直通类型**

  和Value一样，区别在于如果有多个设备绑定这个Action，会发送所有设备的输入

### Control Type控制类型

在这里选择对应的类型，之后在选择对应设备按键相关属性时
会根据你选择内容的不同，筛选对应内容
这上面显示的内容就是各设备属性的返回值类型
当你选择他们后，非选择的类型将不会在之后的按键设置中出现
很多内容我们基本用不到
相当于是在这里筛选输入设备

### Interactions相互作用设置

用于特殊输入，比如长按、多次点击等等
当满足条件时才会触发这个行为（设置长按时间、点击次数等等）

- 开始started
- 触发performed
- 结束canceled

**Processors值处理加工设置**
对得到的值进行处理加工

- **Clamp**
  将输入值钳制到[min..max]范围。
- **Invert**
  反转控件中的值（即，将值乘以-1）。
- **Invert Vector 2**
  反转控件中的值（即，将值乘以-1）。如果invertX为真，则反转矢量的x轴；如果invertY为真，则反转矢量的y轴。
- **Invert Vector 3**
  反转控件中的值（即，将值乘以-1）。如果反转x为真，则反转矢量的x轴；如果反转y为真，则反转y轴；如果反转z为真，则反转z轴。
- **Normalize**
  如果最小值>=零，则将[min..max]范围内的输入值规格化为无符号规格化形式[0..1]，如果最小值<零，则将输入值规格化为有符号规格化形式[-1..1]。
- **Normalize Vector 2**
  将输入向量规格化为单位长度（1）。
- **Normalize Vector 3**
  将输入向量规格化为单位长度（1）
- **Scale**
  将所有输入值乘以系数。
- **Scale Vector 2**
  将所有输入值沿x轴乘以x，沿y轴乘以y。
- **Scale Vector 3**
  将所有输入值沿x轴乘以x，沿y轴乘以y，沿z轴乘以z。
- **Axis Deadzone**
  axis死区处理器缩放控件的值，使绝对值小于最小值的任何值为0，绝对值大于最大值的任何值为1或-1。许多控件没有精确的静止点（也就是说，当控件位于中心时，它们并不总是精确报告0）。在死区处理器上使用最小值可避免此类控件的无意输入。此外，当轴一直移动时，某些控件不一致地报告其最大值。在死区处理器上使用最大值可确保在这种情况下始终获得最大值。
- **Stick Deadzone**
  摇杆死区处理器缩放Vector2控件（如摇杆）的值，以便任何幅值小于最小值的输入向量都将得到（0,0），而任何幅值大于最大值的输入向量都将规格化为长度1。许多控件没有精确的静止点（也就是说，当控件位于中心时，它们并不总是精确地报告0,0）。在死区处理器上使用最小值可避免此类控件的无意输入。此外，当轴一直移动时，某些控件不一致地报告其最大值。在死区处理器上使用最大值可确保在这种情况下始终获得最大值。

### 加号 添加绑定

**Add Binding**
添加新的输入绑定（单按键输入）

**Add Positive\Negative Binding / Add 1D Axis Composite**
添加1D轴组合（类似Input中的水平竖直热键，返回-1~1之间的一个值）

**Add Up\Down\Left\Right Composite / Add 2D Vector Composite**
添加2D向量组合（类似将Input中的水平竖直热键组合在一起，得到的Vector中的x，y分别表示两个轴）

**Add Up\Down\Left\Right\Forward\Backward Composite**
添加3D向量组合

**Add Button With One Modifier Composite**
添加带有一个复合修改器的按钮（可以理解为双组合键， 比如Ctrl+C、Ctrl+V）

**Add Button With Two Modifier Composite**
添加带有两个复合修改器的按钮（可以理解为三组合键，比如Ctrl+K+U）

### 绑定参数

**1D**

按键：

- Negative：负面按键，例如 0~-1
- Positive：正向按键，例如 0~1

绑定设置：

- Composite Type：复合类型

  MinValue：最小值
  MaxValue：最大值

- Which Side Wins：哪一方获胜（当同时按下时如何处理）

  Neither：双方没有优先权，返回MinValue和MaxValue的中间值
  Positive：正面优先，返回maxValue
  Negative：负面优先，返回minValue

**2D**

按键：

- Up:上 ( 0 , 1 )
- Down：下 ( 0 , -1 )
- Left：左 ( -1 , 0 )
- Right：右 ( 1 , 0 )

绑定设置：

- Mode：处理模式
  Analog：模拟值，浮点值
  Digital Normalized：单位化向量
  Digital：未单位化的向量

## Input System Package设置

- **UpdateMode：更新模式**
  - Process Events In Dynamic Update：在动态更新中处理事件
  - Process Events In Fixed Update：在固定更新中处理事件
  - Process Events Manually：手动处理事件
- **Background Behavior：后台行为**
  - Reset And Disable Non Background Devices：重置和禁用非后台设备
  - Reset And Disable All Devices：重置和禁用所有设备
  - Ignore Focus：忽略焦点
- **Filter Noise on .current：过滤当前设备的噪音**
- **Compensate Orientation：定向补偿**
- **Default Deadzone Min：默认死区最小值**
- **Default Deadzone Max：默认死区最大值**
- **Default Button Press Point：默认按钮按下点**
- **Button Release Threshold：按钮释放阈值**
- **Default Tap TIme：默认点击时间**
- **Default Slow Tap Time：默认慢速点击时间**
- **Default Hold Time：默认保持时间**
- **Tap Radius：点击半径**
- **MultTap Delay TIme：多次点击延迟时间**
- **Supported Devices：支持的设备**
  如果希望输入系统支持其可以识别的所有输入设备，请将支持的设备保留为空。
  但是，如果您只对某一组设备感兴趣，那么在此处添加它们将缩小编辑器中显示的范围，并避免从与项目无关的设备获取输入。
  在此处添加设备时，任何未被分类为受支持的设备都将显示在输入调试器的“不受支持的设备”下
- **Motion Usage：动作使用**
- **Description：描述**
- **Play Mode Input Behavior：播放模式输入行为**
  - Pointers And Keyboards Respect Game View Focus：指针和键盘遵守游戏视图焦点
  - All Devices Respect Game View Focus：所有设备都遵守游戏视图焦点
  - All Device Input Always Goes To Game View：所有设备输入始终进入游戏视图

## InputAction类的使用

**启用输入检测**

```c#
public InputAction inputAction;
inputAction.Enable();
```

**操作监听相关**

```c#
//开始操作
inputAction.started += (context)=>{};
//触发
inputAction.performed += (context)=>{};
//结束
move.canceled += (context) =>{};
```

**关键参数 CallbackContext**

```c#
move.performed += (context) =>
{
    //当前状态
    //没有启用 Disabled
    //等待 Waiting
    //开始 Started
    //触发 Performed
    //结束 Canceled
    //context.phase
    print(context.phase);
    //动作行为信息 
    print(context.action.name);
    //控件(设备)信息
    print(context.control.name);
    //获取值
    context.ReadValue<float>();
    //持续时间
    print(context.duration);
    //开始时间
    print(context.startTime);
};
```

## 输入配置文件

- 输入系统中提供了一种输入配置文件
- 可以理解为是InputAction的集合
- 可以在一个文件中编辑多个InputAction的信息

### 创建输入配置文件

在Project窗口右键Create创建InputActions配置文件

### 配置窗口参数相关

 ![](/usr/uploads/2024/07/image-20240730140119641.png)

1. ActionMaps 行动地图窗口（多套操作规范）

   可以理解为配置文件中的行为分组配置
   可以配置多套操作规范

2. Actions 输入动作窗口（InputAction集合）

   可以创建各种InputAciton

3. Properties 输入操作绑定的输入属性（InputAction相关设置）

   设置编辑器中显示哪些控制方案
   Gamepad：手柄
   Joystick：摇杆
   KeyboardMouse：键盘鼠标
   Touch：触屏
   XR：VR/AR等

4. 工具栏 提供保存、搜索等功能按键

   All Devices：设备选择
   当切换了控制方案后
   该控制方案可能是由多个设备配合控制
   可以在这里进一步筛选对应设备

### 根据配置 生成C#代码

1. 选择InputActions文件
2. 在Inspector窗口中设置 生成路径 类名 命名空间
3. 应用后生成代码
4. 在项目中为生成代码中的Action添加事件

## Player Input组件

- PlayerInput是InputSystem提供的专门用于接受玩家输入来处理自定义逻辑的组件
- 通过PlayerInput关联配置文件，它会自动解析该配置文件并关联对应的响应函数，处理对应逻辑

好处：

- 不需要自己进行相关输入的逻辑书写
- 通过配置文件即可配置想要监听的对应行为
- 让我们专注于输入事件触发后的逻辑处理

### PlayerInput参数相关

- **Actions 行为**

  一套输入动作和玩家相关联，帮助我们监听一些按键的输入

  Defatult Control Scheme 默认启用哪个设备控制

  Auto Switch 自动切换设备

  Default Actions Map 默认启用哪一个Map

- **Camrea 关联摄像机**

  当分屏设置时才需要修改此选项

- **UI Input Module**

  关联UI的输入模块

- **Behavior 如何通知游戏对象上执行对应的逻辑**

  - SendMessage 将逻辑脚本挂在到PlayerInput组件同一个对象上，通过SendMessage通知执行对应的函数
  - BroadcastMessage 将逻辑脚本挂在到自身或子对象上，通过SendMessage通知执行对应的函数
  - Invoke UnityEvent Actions 通过拖拽脚本关联函数
  - Invoke Cshap Event 通过C# 事件监听处理对应逻辑

### PlayerInput 事件监听

**Send Messages**

在自定义脚本中申明名为 "On+行为名" 的函数

没有参数 或者 参数类型为InputValue

将该自定义脚本挂载到PlayerInput依附的对象上当触发对应输入时 会自动调用函数

并且还有默认的3个和设备相关的函数可以调用

- 设备注册(当控制器从设备丢失中恢复并再次运行时会触发)：

  OnDeviceRegained(PlayerInput input)

- 设备丢失（玩家失去了分配给它的设备之一，例如，当无线设备耗尽电池时）：

  OnDeviceLost(PlayerInput input)

- 控制器切换：

  OnControlsChanged(PlayerInput input)

**Broadcast Messages** 

基本和SendMessage规则一致

唯一的区别是，自定义脚本不仅可以挂载在PlayerInput依附的对象上

还可以挂载在其子对象下

 **Invoke Unity Events**

在Inspector窗口上通过拖拽的形式关联响应函数

响应函数的参数类型 需要改为 InputAction.CallbackContext

**Invoke C Sharp Events**

```c#
//1.获取PlayerInput组件
var input = GetComponent<PlayerInput>();
//2.获取对应事件进行委托函数添加
input.onDeviceLost += OnDeviceLost;
input.onDeviceRegained += OnDeviceRegained;
input.onControlsChanged += OnControlsChanged;
input.onActionTriggered += OnActionTrigger;
//获取Move的InputAction
input.currentActionMap["Move"].ReadValue<Vector2>()
public void OnDeviceLost( PlayerInput input)
{
    print("设备丢失");
}

public void OnDeviceRegained(PlayerInput input)
{
    print("设备注册");
}

public void OnControlsChanged(PlayerInput input)
{
    print("控制器切换");
}
public void OnActionTrigger(InputAction.CallbackContext context)
{
    switch (context.action.name)
    {
        case "Fire":
            //输入阶段的判断 触发阶段 才去做逻辑
            if(context.phase == InputActionPhase.Performed)
                print("开火");
            break;
        case "Look":
            print("看向");
            print(context.ReadValue<Vector2>());
            break;
        case "Move":
            print("移动");
            print(context.ReadValue<Vector2>());
            break;
    }
}
```

## PlayInputManager组件

 主要用于管理本地多人输入的输入管理器

它主要管理玩家的加入和离开

### 组件添加及参数相关

- Notification Behavior：当玩家进入时PlayerInputManager如何通知关联的对象
  它的工作方式和PlayerInput相同

- Join Behavior：玩家加入的机制

  - Join Players When Button Is Pressed：当有新设备加入按下任意键，或者没有任何玩家时按下任意键
  - Join Players When Join Action Is Triggered：当有新设备加入按下指定按键触发玩家加入
  - Join Players Manually：不要自动加入玩家，需要自己手动加入玩家

- Player Prefab：挂载PlayerInput组件的游戏对象

- Joining Enabled By Default：启用后，新加玩家按照JoinBehavior的规则加入

- Limit Number Of Players：启用后，可以限制加入游戏的玩家数量

  Max Player Count：允许参加游戏的最大玩家数

- Enable Split_Screen：如果启用，会自动为每个对象分配可用屏幕区域的一部分，用于多人分屏游戏

- Maintain Aspect Ratio：假值使游戏能够生成屏幕区域，其纵横比与细分屏幕时的屏幕分辨率不同

- Set Fixed Number：如果该值大于零，则PlayerInputManager始终将屏幕分割为固定数量的矩形，而不考虑实际的玩家数量。

- Screen Rectangle：可用于分配播放器拆分屏幕的规范化屏幕矩形

### PlayerInputManager使用

```c#
//获取PlayerInputManager
//PlayerInputManager.instance
//玩家加入时
PlayerInputManager.instance.onPlayerJoined += (playerInput) =>
{
    print("创建了一个玩家");
};
//玩家离开时
PlayerInputManager.instance.onPlayerLeft += (playerInput) =>
{
    print("离开了一个玩家");
};
```

### InputSystem配合UGUI使用

EventSystem中点击Replace With InputSystemUIInputModel 启用新输入系统

### InputSystem对UI的支持

1. 新输入系统InputSystem不支持IMGUI（GUI）注意：编辑器代码不受影响
2. 如果当前激活的是InputSystem，那么OnGUI中的输入判断相关内容不会被触发
3. 必须要选择Both或者只激活老输入系统InputManager才能让OnGUI中内容有用
4. .新输入系统支持UGUI，但是需要使用新输入系统输入模块（Input System UI Input Module）

### Input System UI Input Module 参数

- **Move Repeat Delay**

  生成初始IMoveHandler之间的初始延迟（秒）。

  OnMove导航事件，并在移动操作保持激活状态时生成重复的导航事件

- **Move Repeat Rate**

  移动动作保持激活时，生成重复导航事件之间的间隔（秒）。

  此参数由帧速率限制，每帧不会有多个移动重复事件触发。

- **XR Tracking Orgin**

- **Deselect On Background Click**

  默认情况下，当指针被点击并且没有击中任何游戏对象时，当前对象的选中状态会被清除

  取消勾选此选项时，对象的选中状态不会被清除

- **Pointer Behavior**

  如何处理输入UI的多个指针

  - Single Mouse Or Pen BUt Multi Touch And Track
    对于未分类为触摸或跟踪输入的所有输入，其行为类似于单个统一指针，对于跟踪和触摸输入，其行为类似于所有指针。
    例如，如果在鼠标和笔上接收到并发输入，则将两者的输入馈送到同一UI指针实例中。其中一个的位置输入将覆盖另一个的位置。
    请注意，当从触摸或跟踪设备接收到输入时，鼠标和笔的单个统一指针将被删除，包括在鼠标/笔光标当前悬停在对象上时发送的IPointerExit事件。
  - Single Unified Pointer
    所有指针输入都是统一的，因此只有一个指针。这包括触摸和跟踪输入。例如，这意味着，无论有多少设备将输入输入输入点，只有帧中最后一个这样的输入才会生效并成为当前UI指针的位置。
  - All Pointers As Is
    UI输入模块不会统一任何指针输入。任何设备，包括提供输入指针类型动作的触摸和跟踪设备，都将是其自己的指针（或触摸输入的多个指针）。
    注意：这可能意味着UI中将有任意数量的指针，并且可能同时指向多个对象。

- **Actions Asset**
  包含控制UI的所有操作的输入操作资产。您可以使用以下属性选择资产中的哪些操作对应于哪些UI输入。
  默认情况下，这将引用名为DefaultInputActions的内置资产，该资产包含用于驱动UI的常见默认操作。如果要设置自己的操作，请创建自定义输入操作资源并在此处分配。在Inspector中将新资源引用指定给此字段时，编辑器会尝试根据常用命名约定自动将操作映射到UI输入。

- **Point**
  提供2D屏幕位置的动作。用作指向UI元素的光标，以实现鼠标样式的UI交互。
  设置为传递操作类型和向量2值类型。

- **Left Click**
  映射到用于与UI交互的主光标按钮的操作。
  设置为传递操作类型和按钮值类型。

- **Middle Click**
  映射到用于与UI交互的中间光标按钮的操作
  设置为传递操作类型和按钮值类型。

- **Right Click**
  映射到用于与UI交互的辅助光标按钮的操作。
  设置为传递操作类型和按钮值类型。

- **Scroll Wheel**
  提供手势输入以允许在UI中滚动的操作。
  设置为传递操作类型和向量2值类型。

- **Move**
  一种操作，提供用于选择当前活动用户界面的二维矢量。这允许游戏板或箭头键样式的UI导航。
  设置为传递操作类型和向量2值类型

- **Submit**
  与当前选择的UI接触或“单击”的操作。
  设置为按钮动作类型。

- **Cancel**
  退出与当前选定UI的任何交互的操作。
  设置为按钮动作类型。

- **Tracked Position**
  提供一个或多个空间跟踪设备（如XR hand控制器）的3D位置的动作。结合跟踪设备方向，这允许通过指向空间中的UI可选择项进行XR样式的UI交互。
  设置为传递操作类型和向量3值类型。

- **Tracked Orientation**
  传递表示一个或多个空间跟踪设备（如XR hand控制器）旋转的四元数的操作。结合跟踪设备位置，这允许通过指向空间中的UI可选择项进行XR样式的UI交互。
  设置为传递操作类型和四元数值类型。

### VR项目使用新输入系统注意事项

需要在Canvas对象上添加Tracked Device Raycaster组件

### 多人游戏使用多套UI

如果同一设备上的多人游戏，每个人想要使用自己的一套独立UI

需要将EventSystem中的EventSystem组件替换为Multiplayer Event System组件

与EventSystem组件不同，可以在场景中同时激活多个MultiplayerEventSystem。

这样，您可以有多个玩家，每个玩家都有自己的InputSystemUIInputModule和MultiplayerEventSystem组件

每个玩家都可以有自己的一组操作来驱动自己的UI实例。

如果您正在使用PlayerInput组件，还可以设置PlayerInput以自动配置玩家的InputSystemUIInputModule以使用玩家的操作

MultilayerEventSystem组件的属性与事件系统中的属性相同

此外，MultiplayerEventSystem组件还添加了一个playerRoot属性，您可以将其设置为一个游戏对象

该游戏对象包含此事件系统应在其层次结构中处理的所有UI可选择项

### On-Screen组件相关

On-Screen组件可以模拟UI和用户操作的交互

1. On-Screen Button：按钮交互
2. On-Screen Stick：摇杆交互

## Input Debug

InputDebug可以用来排查问题 当输入不按预期工作时

### 打开Input Debug窗口

1. Window(窗口)->Analysis(分析)->Input Debugger(输入调试器)
2. PlayerInput组件->Open Input Debugger

### 窗口上的信息

- Add Devices Not Listed in Supported Devices
  添加未在支持的设备中列出的设备
  Enable Event Diagnostics
  启用事件诊断
  Simulate TOuch Input From Mouse or pen
  模拟鼠标或笔的触摸输入
- Remote Devices
  远程设备
- Devices
  系统中当前所有输入设备列表
  Unsupported
  不支持、无法识别的设备列表
- Layouts
  设备布局列表
  所有已注册的设备控制列表
  Abstract Devices
  抽象设备
  Specific Devices
  特定设备
- Settings
  设置相关
  默认的一些特殊输入设置
- Metrics
  指标相关
  输入系统资源使用情况的统计信息

## InputSystem 任意键按下的方法

```c#
//如果用Call 按键盘会报错 但是可以执行
//CallOnce 指挥执行一次 但不会报错
InputSystem.onAnyButtonPress.CallOnce((inputControll)=>{
    Debug.log("inputControll.name")
});
```

## 从Json读取InputActionAsset

```c#
PlayerInput playerInput = GetComponent<PlayerInput>();
//读取json文本
string str = Resources.Load<TextAsset>("Test").text;
InputActionAsset asset = InputActionAsset.FromJson(str);
playerInput.actions = asset; 
playerInput.actions.Enable();
```

