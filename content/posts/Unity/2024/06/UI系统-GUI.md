---
title: "UI系统-GUI"
author: "mona"
date: 2024-06-17T11:47:00+08:00
categories: ["Unity"]
tags: ["GUI"]
---

### GUI是什么

- 全称 即时模式游戏用户交互界面（IMGUI）
- 在Unity中一般简称为GUI 他是一个代码驱动的UI系统

### GUI的作用

- 作为程序员的调试工具，创建游戏内的调试工具
- 为脚本组件创建自定义监视面板
- 创建新的编辑器窗口和工具 以及拓展unity本身（一般用作内置游戏工具）
- 不要用它为玩家制作ui功能

### GUI的工作原理

- 在继承Mono的脚本中的特殊函数里
- 调用GUI提供的方法 类似生命周期函数

```c#
private void OnGUI(){
    //GUI的相关代码
}
```

### OnGUI

- 它每帧执行 相当于是专门绘制GUI界面的函数
- 一般只在其中执行GUI相关界面绘制和操作逻辑
- 该函数在OnDisable之前 LateUpdate之后执行
- 只要是继承Mono的脚本 都可以在OnGUI中绘制GUI

### GUI基本控件

#### GUI控件绘制的共同点

- 他们都是GUI公共类中提供的静态函数 直接调用即可
- 他们的参数都大同小异
  - 位置参数：Rect参数 xy位置 wh尺寸
  - 显示文本：string 参数
  - 图片信息：Texture参数
  - 综合信息：GuiConten参数
  - 自定义样式：GUIStyle参数
- 每一种控件都有多种重载，都是各个参数的排列组合，必备参数是 位置信息和显示信息

#### 文本控件

```c#
//创建一个 位置为0 0 宽带100 高度30的文本控件
GUI.Label(new Rect(0,0,100,30), "我爱吃肉");
//可在面板上设置参数
public Rect rect;
public GUIContent content;
public GUIStyle style;//字体样式
GUI.Label(rect, content,style);
```

#### 按钮控件

```c#
//创建一个 位置为0 0 宽带100 高度30的按钮 返回float
GUI.Button(new Rect(0,0,100,30), "我爱吃肉");
//可在面板上设置参数
public Rect rect;
public GUIContent content;
public GUIStyle style;//字体样式
if(GUI.Button(rect, content,style)){
    Debug.Log("按钮被点击");
}
//长按按钮
if(GUI.RepeatButton(rect, content,style)){
    Debug.Log("按钮被长按");
}
```

#### 多选框

```c#
private bool isSel;
//第二个参数bool  多选框的状态bool 返回值是否被点击
isSel = GUI.Toggle(rect,isSel,"效果开关");

//一组单选实现
int nowSelIndex = 0;
if(GUI.Toggle(rect,nowSeleIndex==1;,"效果开关1")){
    //如果被选中nowSeleIndex等于1
    nowSeleIndex==1;
}
if(GUI.Toggle(rect,nowSeleIndex==2;,"效果开关2")){
    //如果被选中nowSeleIndex等于1
    nowSeleIndex==2;
}
if(GUI.Toggle(rect,nowSeleIndex==3;,"效果开关3")){
    //如果被选中nowSeleIndex等于1
    nowSeleIndex==3;
}
```

#### 输入框

```c#
public Rect rect;
string inputText;
//参数里的string是显示的内容  int最大长度
inputText = GUI.TextField(rect,inputText,5);
//密码输入
string inputPW;
//*替代字符
inputPW = GUI.PasswordField(rect,inputPW,'*')
```

#### 拖动条

```c#
//水平拖动条
public Rect rect;
float sliderValue;
//第一个float 当前的值
//第2个和第3个float 最小值和最大值
GUI.HorizontalSlider(rect,sliderValue,0f,100f);
//垂直拖动条
GUI.VerticalSlider(rect,sliderValue,0f,100f);
```

#### 绘制图片

```c#
public Rect rect;
public Texture texture;//图片
GUI.DrawTexture(rect,texture);
public bool alpha = true;//是否启用透明度
public ScaleMode mode = ScaleMode.StretchToFill;//缩放模式
public float wh =0; //自定义宽高比
GUI.DrawTexture(rect,texture,mode,alpha,wh);
```

#### 框绘制

```c#
public Rect rect;
//可以在框中显示文字
GUI.Box(rect,"123");
```

### GUI符合控件

#### 工具栏

```c#
private int toolbarIndex = 0;
private string[] toolbarInfos = new string[]{"选项1","选项2","选项3"};
public Rect rect;
toolbarIndex = GUI.Toolbar(rect,toolbarIndex,toolbarInfos);
```

#### 选择网络

```c#
public Rect rect;
private int selectionGridIndex = 0;
private string[] SelectionGridInfos = new string[]{"选项1","选项2","选项3"};
//2 一行最多显示2个
selectionGridIndex = GUI.SelectionGrid(rect,selectionGridIndex,SelectionGridInfos,2)
```

#### 控件分组

- 用于批量控制控件的位置
- 可以理解为 包裹着的控件加了一个父对象
- 可以通过控制分组来控制包裹控件的位置

```c#
public Rect groupPos;
GUI.BeginGroup(groupPos);
GUI.Button(new Rect(0,0,100,30)."按钮1");
GUI.Button(new Rect(30,0,100,30)."按钮2");
GUI.Button(new Rect(60,0,100,30)."按钮3");
GUI.EndGroup(groupPos);
```

#### 滚动列表

```C#
public Vector2 nowPos；//内容的当前位置
public Rect scPos;//ScrollView的位置和大小
public Rect showPos;//内容的位置和尺寸 要比ScrollView大
nowPos = GUI.BeginScrollView(scPos,nowPos,showPos)
private string[] toolbarInfos = new string[]{"选项1","选项2","选项3","按钮4"};
GUI.Toolbar(new Rect(0,0,300,50),toolbarInfos,0);
GUI.Toolbar(new Rect(0,60,300,50),toolbarInfos,0);
GUI.Toolbar(new Rect(0,120,300,50),toolbarInfos,0);
GUI.Toolbar(new Rect(0,180,300,50),toolbarInfos,0);
GUI.EndScrollView();
```

#### 窗口

```c#
public Rect rect;

//1是窗口id
GUI.Window(1,rect,DrawWindow,"测试窗口")   
    
GUI.Window(2,rect,DrawWindow,"测试窗口")      
//委托函数 里面是窗口里的内容
//rect以窗口里的0，0为远点
private void DrawWindow(int id){ //id=1
	GUI.Button(rect,"按钮")
}
```

#### 模态窗口

- 模态窗口 可以让窗口以外的控件失效
- 可以理解为窗口再最上层

```c#
GUI.ModalWindow(1,rect,DrawWindow,"模态窗口");
```

#### 拖动窗口

```c#
public Rect rect;
public Rect windowPos;
//窗口返回值Rest
windowPos = GUI.Window(1,windowPos,DrawWindow,"测试窗口");   
private void DrawWindow(int id){ //id=1
	GUI.Button(rect,"按钮")
     //该ip写在窗口的委托函数中调用 可以让窗口被拖动
     GUI.DrageWindow();
    //重载 可以决定窗口中哪一部分位置可以被拖动 默认所有位置都可以被拖动
    GUI.DrageWindow(new Rect(0,0,1000,20));
}
```

### 自定义皮肤样式

#### 全局颜色

```c#
//全局的着色 影响背景和文本的颜色
//不常用 添加了style也会呗覆盖
GUI.color = Color.red;
GUI.contentColor = Color.yellow;//文本颜色 颜色会和全局着色相乘
GUI.backgroundColor = Color.yellow;//背景颜色 颜色会和全局着色相乘
GUI.Button(rect,"red按钮")
GUI.color = Color.white;//可以再写一个来修改
GUI.Button(rect,"white按钮")
```

#### 整体皮肤样式

```c#
//全局的皮肤样式 默认是null
//可以去创建GUI Skin文件 里面是各种控件的gui style
GUI.skin = null;
```

### GUILayout自动布局

- GUILayout可以点出GUI中的各种控件
- 会自动布局 自动调整控件的大小 不需要传入Rect
- 主要用于编辑器开发
- 可以和GUI配合使用

```c#
GUILayout.BeginHorizontal(new Rect(100,100,100,100));//水平绘制 可以修改大小范围等等
GUILayout.Button("测试按钮");
GUILayout.Button("测试按钮");
GUILayout.Button("测试按钮");
GUILayout.EndHorizontal();
```

#### GUILayoutOption布局规则

```c#
GUILayout.Button("测试按钮"，GUILayoutOption可变参数可传入多个);
//控件的固定宽高
GUILayoutOption.Width(300);
GUILayoutOption.Height(200);
//允许控件的最小宽高
GUILayout.MinWidth(50);
GUILayout.MinHeight(50);
//允许控件的最大宽高
GUILayout.MaxWidth(100);
GUILayout.MaxHeight(100);
//允许或禁止水平拓展
GUILayout.ExpandWidth(true);//允许
GUILayout.ExpandHeight(false);//禁止
GUILayout.ExpandHeight(true);//允许
GUILayout.ExpandHeight(false);//禁止
```

