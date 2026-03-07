---
title: "Unity编辑器扩展-窗口扩展"
author: "mona"
date: 2024-08-08T15:08:00+08:00
categories: ["Unity"]
tags: ["编辑器扩展"]
---

## 特殊文件夹Editor

Editor文件夹是Unity中的特殊文件夹

所有Unity中编辑器相关的脚本都需要放置在其中

用了Unity编辑器相关命名空间的脚本最终是不能被打包出去

需要把这些脚本放置到Editor文件夹中，避免打包时报错

## 菜单栏扩展

**在Unity菜单栏中添加自定义页签**

命名空间：UnityEditor

特性：MenuItem

用法：

在静态函数前加上[MenuItem("页签/一级选项/二级选项/....")]

注意：

1. 不需要继承mono
2. 斜杠必须是/

```c#
[MenuItem("Unity编辑器拓展/Lesson1/TestFun %#&A")]
private static void TestFun()
{
    Debug.Log("TestFun");
}
```

**在Hierarchy窗口中添加自定义页签**

```c#
[MenuItem("GameObject/Lesson1/TestFun2")]
private static void TestFun2()
{
    Debug.Log("TestFun2");
}
```

**在Project窗口中添加自定义页签**

```c#
[MenuItem("Assets/Lesson1/TestFun3")]
private static void TestFun3()
{
    Debug.Log("TestFun3");
}
```

**在Inspector为脚本右键添加菜单**

```c#
[MenuItem("CONTEXT/脚本名/Lesson1/自定义功能")]
private static void TestFun4()
{
    Debug.Log("TestFun4");
}
```

**加入快捷键**

单键

路径后 + 空格 + 下划线 + 想要的按键

组合键

下划线替换为

- %表示ctrl
- \#表示shift
- &表示alt

其他支持的按键：

LEFT、RIGHT：持类似#LEFT是左shift之类的按键

UP、DOWN、F1..F12、HOME、END、PGUP、PGDN

**在菜单栏的Component菜单添加脚本**

命名空间：UnityEngine

特性：AddComponentMenu

用法：

在想要通过Component菜单添加的脚本前加上

[AddComponentMenu("一级选项/二级选项/....")]

注意：

1. 脚本需要继承MonoBehaviour对象
2. 斜杠必须是/
3. 最后一级选项的名字可以和脚本名不一样，但是建议一致

```c#
[AddComponentMenu("Unity编辑器拓展/添加脚本/Lesson1_Test")]
public class Lesson1_Test : MonoBehaviour
{
}
```

## 创建窗口

当我们想要为Unity拓展一个自定义窗口时

需要实现一个继承了EditorWindow的类

并且在该类的OnGUI函数中编写面板控件相关的逻辑

```c#
public class Test : EditorWindow
{
    [MenuItem("Unity编辑器拓展/Lesson2/显示自定义面板")]
    private static void ShowWindow()
    {
        //开启窗口
        Lesson2 win = EditorWindow.GetWindow<Lesson2>();
        win.titleContent = new GUIContent("我的窗口");
        win.Show();
    }
}
```

**EditorWindow.GetWindow（）方法**

该方法有很多种重载

其中的主要参数一般有

- Type或T：窗口类的类型
- utility：为 true 可创建浮动实用程序窗口，设置为 false 可创建正常窗口。所谓浮动窗口就是可以自由拖动自由改变大小
- title：窗口标题
- focus：是否为窗口提供焦点（如果已存在）。（如果 GetWindow 创建新窗口，则将始终获得焦点）
- desiredDockNextTo:窗口试图停靠到其上的 EditorWindow 类型的数组

创建窗口对象之后调用对象的Show方法即可显示窗口

### 窗口事件回调函数

继承EditorWindow的窗口类 自带一些事件回调函数

当触发对应事件时会自动进入

- OnHierarchyChange():当场景中的层次结构（Hierarchy）发生变化时调用。例如，当游戏对象被创建、删除或重命名时触发。
- OnFocus():当窗口获得焦点时调用。在这个方法中，你可以执行一些在窗口获得焦点时需要进行的操作。
- OnLostFocus():当窗口失去焦点时调用。 通常在这个方法中执行一些在窗口失去焦点时需要进行的清理工作。
- OnProjectChange():当项目资源发生变化时调用。 例如，当添加、删除或修改项目中的文件时触发。
- OnInspectorUpdate():在检视器（Inspector）面板更新时调用。可以在这个方法中执行需要在检视器面板刷新时进行的逻辑，比如更新显示的信息
- OnSelectionChange():当选择的对象发生变化时调用。 在这个方法中，你可以执行与所选对象相关的操作，以确保编辑器窗口的内容与当前选择保持同步。

```c#
private void OnHierarchyChange()
{
    Debug.Log("当Hierarchy窗口内容发生变化时");
}

private void OnFocus()
{
    Debug.Log("获取焦点");
}

private void OnLostFocus()
{
    Debug.Log("失去焦点");
}

private void OnProjectChange()
{
    Debug.Log("当Project窗口内容发生变化时");
}

private void OnInspectorUpdate()
{
    Debug.Log("当Inspector窗口内容发生变化时");
}

private void OnSelectionChange()
{
    Debug.Log("当选中对象发生变化时");
}
```

### 窗口中常用的生命周期函数

- OnEnable():当窗口被激活时调用，通常在窗口创建时会调用一次。在这个方法中，你可以进行一些初始化工作，例如注册事件监听器或设置初始变量。
- OnGUI():每帧都会调用此方法，用于绘制编辑器窗口的 GUI。在这个方法中，你可以使用 GUILayout 或 EditorGUILayout 等类创建界面元素，以便用户与窗口进行交互。
- OnDestroy():当窗口被销毁时调用，通常在关闭编辑器或切换场景时触发。在这里进行最终的清理工作，确保没有未释放的资源。
- Update():在编辑器窗口每帧更新时调用。通常在这里执行一些需要在每帧进行的逻辑

```c#
private void OnGUI()
{
    GUILayout.Label("测试文本");
    if(GUILayout.Button("测试按钮"))
    {
        Debug.Log("Test");
    }
}
```

### 编辑器窗口类中的常用成员

Unity官方文档有关于编辑器窗口类的API说明

https://docs.unity.cn/cn/2022.3/ScriptReference/EditorWindow.html

**静态变量**

- focusedWindow：当前已获得键盘焦点的 EditorWindow。（只读）
- mouseOverWindow：当前在鼠标光标下的 EditorWindow。（只读）

**静态函数**

- CreateWindow: 创建窗口，如果允许一个窗口有多个可以用该API创建窗口
- GetWindow: 通过它我们可以创建一个窗口对象
- GetWindowWithRect：返回一个指定位置、大小的窗口
- HasOpenInstances：检查编辑器窗口是否打开

**成员变量**

- titleContent：窗口标题名
- positon：窗口位置大小信息
- wantsMouseEnterLeaveWindow:如果设置为 true，则每当鼠标进入或离开窗口时，该窗口都会收到一次 OnGUI 调用

**成员函数**

- Show: 显示面板
- Repaint：重绘窗口
- Close: 关闭窗口

## EditorGUI

EditorGUI 类似 GUI

主要用于绘制编辑器拓展 UI 的工具类

而EditorGUILayout 类似于 GUILayout

是一个带有自动布局功能的 EditorGUI 绘制工具类

我们经常会将 EditorGUI 和 GUI 混合使用 来制作一些编辑器拓展功能

但是由于更多时候我们会用到自动布局功能

EditorGUI和它的区别仅仅是需要自己设置位置而已

详细内容：https://docs.unity.cn/cn/2022.3/ScriptReference/EditorGUILayout.html

### EditorGUILayout使用

**文本控件**

```c#
EditorGUILayout.LabelField("文本标题", "测试内容");
EditorGUILayout.LabelField("文本内容");
```

**层级和标签选择控件**

```c#
int layer;
string tag;
private void OnGUI()
{
    layer = EditorGUILayout.LayerField("层级选择", layer);
    tag = EditorGUILayout.TagField("标签选择", tag);
}
```

**颜色获取控件**

```c#
Color color;
private void OnGUI()
{
   color = EditorGUILayout.ColorField(new GUIContent("标题"), color, true, false, false); 
    //color变量 = EditorGUILayout.ColorField(new GUIContent("标题"), color变量, 是否显示拾色器, 是否显示透明度通道, 是否支持HDR);
}

```

**枚举选择**

```c#
public enum E_TestType
{
    One = 1,
    Two = 2,
    Three = 4,
    One_and_Two = 1 | 2,
}
E_TestType type;
E_TestType type2;
private void OnGUI()
{
    type = (E_TestType)EditorGUILayout.EnumPopup("枚举选择", type);
    type2 = (E_TestType)EditorGUILayout.EnumFlagsField("枚举多选", type2);
}
```

**整数选择**

```c#
string[] strs = { "选择123", "选择234", "选择345" };
int[] ints = { 123, 234, 345 };
int num = 0;
private void OnGUI()
{
    //整数选择控件
    //返回值 其实是整数数组当中的某一个值
    num = EditorGUILayout.IntPopup("整数单选框", num, strs, ints);
    EditorGUILayout.LabelField(num.ToString());
}
```

**按下就响应的按钮**

```c#
if (EditorGUILayout.DropdownButton(new GUIContent("按钮上文字"), FocusType.Passive))
{
    Debug.Log("按下就响应");
}
```

**对象关联**

```c#
GameObject obj;
private void OnGUI()
{
    //对象关联
    obj = EditorGUILayout.ObjectField("关联资源对象", obj, typeof(GameObject), false) as GameObject;
}
```

**各类型输入**

```c#
int i;
long l;
float f;
double d;
string str;
Vector2 vec2;
Vector3 vec3;
Vector4 vec4;
Rect rect;
Bounds bounds;
BoundsInt boundsInt;
private void OnGUI()
{
    //各类型输入
    i = EditorGUILayout.IntField("Int输入框", i);
    EditorGUILayout.LabelField(i.ToString());
    l = EditorGUILayout.LongField("long输入框", l);
    f = EditorGUILayout.FloatField("Float 输入：", f);
    d = EditorGUILayout.DoubleField("double 输入：", d);

    str = EditorGUILayout.TextField("Text输入：", str);
    vec2 = EditorGUILayout.Vector2Field("Vec2输入： ", vec2);
    vec3 = EditorGUILayout.Vector3Field("Vec3输入： ", vec3);
    vec4 = EditorGUILayout.Vector4Field("Vec4输入： ", vec4);

    rect = EditorGUILayout.RectField("rect输入： ", rect);
    bounds = EditorGUILayout.BoundsField("Bounds输入： ", bounds);
    boundsInt = EditorGUILayout.BoundsIntField("Bounds输入： ", boundsInt);

    //注意：EditorGUILayout中还有一些Delayed开头的输入控件
    //     他们和普通输入控件最主要的区别是：在用户按 Enter 键或将焦点从字段移开之前，返回值不会更改
    i2 = EditorGUILayout.DelayedIntField("Int输入框", i2);
    EditorGUILayout.LabelField(i2.ToString());
}
```

**折叠、折叠组**

```c#
bool isHide,isHideGroup;
private void OnGUI()
{
    //折叠控件
    isHide = EditorGUILayout.Foldout(isHide, "折叠控件", false);
    if(isHide)
    {
        //控件
    }
	//折叠组控件
    isHideGroup = EditorGUILayout.BeginFoldoutHeaderGroup(isHideGroup, "折叠组控件");
    if(isHideGroup)
    {
        //控件
    }
    EditorGUILayout.EndFoldoutHeaderGroup();
}
```

**开关、开关组**

```c#
bool isTogGroup,isTog,isTogLeft;
private void OnGUI()
{
    //开关组控件
    isTogGroup = EditorGUILayout.BeginToggleGroup("开关组控件", isTogGroup);
    //开关控件
    isTog = EditorGUILayout.Toggle("开关控件", isTog);
    isTogLeft = EditorGUILayout.ToggleLeft("左侧开关", isTogLeft);
    EditorGUILayout.EndToggleGroup();//开关组结束
}
```

**滑动条**

```c#
float fSlider,leftV,rightV;
int iSlider;
private void OnGUI()
{
    fSlider = EditorGUILayout.Slider("滑动条", fSlider, 0, 10);
    iSlider = EditorGUILayout.IntSlider("整形滑动条", iSlider, 0, 10);
    //双块滑动条
    EditorGUILayout.MinMaxSlider("双块滑动条", ref leftV, ref rightV, 0, 10);
    EditorGUILayout.LabelField(leftV.ToString());
    EditorGUILayout.LabelField(rightV.ToString());
}
```

**帮助框控件**

```c#
private void OnGUI()
{
    EditorGUILayout.HelpBox("一般提示", MessageType.None);
    EditorGUILayout.Space(10);
    EditorGUILayout.HelpBox("感叹号提示", MessageType.Info);
    EditorGUILayout.Space(50);
    EditorGUILayout.HelpBox("警告符号提示", MessageType.Warning);
    EditorGUILayout.Space(100);
    EditorGUILayout.HelpBox("错误符号提示", MessageType.Error);
}
```

**间隔控件**

```c#
private void OnGUI()
{
    EditorGUILayout.Space(10);
}
```

**动画曲线控件**

```c#
AnimationCurve curve = new ();
private void OnGUI()
{
    curve = EditorGUILayout.CurveField("动画曲线：", curve);
}
```

**布局相关API**

```c#
Vector2 pos;
private void OnGUI()
{
    EditorGUILayout.BeginHorizontal(); //开始水平布局
    //控件
    EditorGUILayout.EndHorizontal();//结束水平布局

    EditorGUILayout.BeginVertical();//开始垂直布局
    //控件
    EditorGUILayout.EndVertical();//结束垂直布局

    pos = EditorGUILayout.BeginScrollView(pos); //开启滚动视图
    //控件
    EditorGUILayout.EndScrollView(); //结束滚动视图
}
```

## EditorGUIUtility公共类

Utility是实用的意思，EditorGUIUtility 是 EditorGUI 中的一个实用工具类

提供了一些 EditorGUI 相关的其他辅助API

### Editor Default Resources文件夹

Editor Default Resources 也是Unity当中的一个特殊文件夹

它的主要作用是放置提供给 EditorGUIUtility 加载的资源

想要使用EditorGUIUtility公共类来加载资源我们需要将资源放置在 Editor Default Resources 文件夹中

### EditorGUIUtility API

**加载资源**

```c#
private Texture img;
private Texture img2;
private void OnGUI()
{
    //加载资源（如果资源不存在返回null)
    if(GUILayout.Button("加载编辑器图片资源"))
        img = EditorGUIUtility.Load("EditorTeach.png") as Texture;
    if (img != null)
        GUI.DrawTexture(new Rect(0, 50, 160, 90), img);

    //加载资源（如果资源不存在会直接报错）
    if (GUILayout.Button("加载编辑器图片资源"))
        img2 = EditorGUIUtility.LoadRequired("EditorTeach.png") as Texture;
    if (img2 != null)
        GUI.DrawTexture(new Rect(0, 150, 160, 90), img2);
}
```

**搜索框查询**

主要作用：

弹出一个搜索窗口，用于选择自己想要的资源

```c#
Texture img;
private void OnGUI()
{ 
    if(GUILayout.Button("打开搜索框查询窗口"))
    {
         EditorGUIUtility.ShowObjectPicker<资源类型>(默认被选中的对象, 是否允许查找场景对象, "查找对象名称过滤", 0);
    }
    //通过Event公共类可以获取其它窗口发送给自己的事件
    //Event.current 获取当前事件 commandName 获取事件命令的名字
    if(Event.current.commandName == "ObjectSelectorUpdated")
    {
        //当选择发生更新时
        img = EditorGUIUtility.GetObjectPickerObject() as Texture;
    }
    else if(Event.current.commandName == "ObjectSelectorClosed")
    {
        //当窗口关闭时
        img3 = EditorGUIUtility.GetObjectPickerObject() as Texture;
    }

    //对象选中提示提示
    if(GUILayout.Button("高亮选中对象"))
    {
        if (img != null) EditorGUIUtility.PingObject(img3);//对象选中提示
    }

}
```

**窗口事件传递**

```c#
if(GUILayout.Button("传递事件"))
{
    //声明事件
    Event e = EditorGUIUtility.CommandEvent("事件");
    Lesson3 win = EditorWindow.GetWindow<Lesson3>();
    win.SendEvent(e);
}

if(Event.current.type == EventType.ExecuteCommand)
{
    if(Event.current.commandName == "事件")
    {
        Debug.Log("事件");
    }
}
```

**GUI坐标转换**

```c#
if(GUILayout.Button("坐标转换测试"))
{
    Vector2 v = new Vector2(10, 10);
    GUI.BeginGroup(new Rect(10, 10, 100, 100));
    //转换函数 如果包裹在布局相关函数中 那么位置胡加上布局的偏移 再进行转换
    Vector2 screenPos = EditorGUIUtility.GUIToScreenPoint(v);
    GUI.EndGroup();
    Debug.Log("GUI:" + v + "Screen:" + screenPos);
}
```

**在指定区域使用其他鼠标指针**

```c#
EditorGUI.DrawRect(new Rect(0, 150, 100, 100), Color.green);//绘制一个绿色区域
EditorGUIUtility.AddCursorRect(new Rect(0, 150, 100, 100), MouseCursor.Text);
//  MouseCursor鼠标光标类型枚举
//  Arrow	            普通指针箭头
//  Text                文本文本光标
//  ResizeVertical      调整大小垂直调整大小箭头
//  ResizeHorizontal    调整大小水平调整大小箭头
//  Link                带有链接徽章的链接箭头
//  SlideArrow          滑动箭头带有小箭头的箭头，用于指示在数字字段处滑动
//  ResizeUpRight       调整大小向上向右调整窗口边缘的大小
//  ResizeUpLeft        窗口边缘为左
//  MoveArrow           带有移动符号的箭头旁边用于场景视图
//  RotateArrow         旁边有用于场景视图的旋转符号
//  ScaleArrow          旁边有用于场景视图的缩放符号
//  ArrowPlus           旁边带有加号的箭头
//  ArrowMinus          旁边带有减号的箭头
//  Pan                 用拖动的手拖动光标进行平移
//  Orbit               用眼睛观察轨道的光标
//  Zoom                使用放大镜进行缩放的光标
//  FPS                 带眼睛的光标和用于FPS导航的样式化箭头键
//  CustomCursor        当前用户定义的光标
//  SplitResizeUpDown   向上-向下调整窗口拆分器的大小箭头
//  SplitResizeLeftRight窗口拆分器的左-右调整大小箭头
```

 **绘制色板、绘制曲线**

```c#
//绘制色板
color = EditorGUILayout.ColorField(new GUIContent("选取颜色"), color, true, true, true);

EditorGUIUtility.DrawColorSwatch(new Rect(180, 180, 30, 30), color);

//绘制曲线
curve = EditorGUILayout.CurveField("曲线设置", curve);
EditorGUIUtility.DrawCurveSwatch(new Rect(0, 300, 100, 80), curve, null, Color.red, Color.white);
#endregion
```

## Selection 公共类

主要用来获取当前在Unity编辑器中选择的对象

只能用于编辑器开发中

### 静态成员

**获取当前选中的单个对象**

未选择返回Null

选择多个则返回第一个选择的游戏物体

```c#
Selection.activeObject;//获取当前选择的Object
Selection.activeGameObject;//获取当前选择的GameObject
Selection.activeTransform;//获取当前选择的Transform
```

**获取当前选择的所有对象**

未选择返回Null

```c
Selection.objects;
Selection.gameObjects;
Selection.transforms;
```

### 常用静态方法

**对象是否被选中**

```c#
if (Selection.Contains(obj))
    Debug.Log("对象有被选中");
else
    Debug.Log("对象没有被选中");
```

**筛选对象**

```c#
Object[] objs = Selection.GetFiltered(typeof(Object), SelectionMode.Assets | SelectionMode.DeepAssets);
Object[] objs = Selection.GetFiltered<Object>(SelectionMode.Assets | SelectionMode.DeepAssets);
//  筛选模式：SelectionMode
//  Unfiltered: 不过滤
//  TopLevel: 只获取最上层对象，子对象不获取
//  Deep: 父对象、子对象都获取
//  ExcludePrefab: 排除预设体
//  Editable: 只选择可编辑的对象
//  OnlyUserModifiable: 仅用户可修改的内容
//  Assets: 只返回资源文件夹下的内容
//  DeepAssets: 如果存在子文件夹，其中的内容也获取
//  如果要混用 位或 | 即可
```

**当选中变化时会调用的委托**

```c#
Selection.selectionChanged += 函数;//选择的物体变化时调用
```

## Event公共类

它提供了许多属性和方法，允许你检查和处理用户输入

主要用于在Unity编辑器拓展开发中

因为Input相关内容需要在运行时才能监听输入

而Event专门提供给编辑模式下使用，可以帮助我们检测鼠标键盘输入等事件相关操作

在 OnGUI 和 OnSceneView 中都能使用

### Event API

```c#
Event.current;//获取当前输入事件
Event.current.alt;//alt键是否按下
Event.current.shift;//shift键是否按下
Event.current.control;//ctrl键是否按下
Event.current.isMouse;//是否是鼠标事件
Evnet.current.button (0);//判断鼠标左中右键0,1,2 分别代表 左,右,中 如果大于2可能是其他鼠标按键
Event.curretn.mousePosition;//鼠标位置
Event.current.isKey;//判断是否是键盘输入
Event.current.character;//获取键盘输入的字符
Event.current.keyCode;//获取键盘输入对应的KeyCode]
//判断输入类型
Event.current.type//EventType枚举和它比较即可EventType中有常用的 鼠标按下抬起拖拽，键盘按下抬起等等类型
Event.current.commandName//键盘事件 字符串
//  可以用来判断是否触发了对应的键盘事件
//  返回值：
//  Copy:拷贝
//  Paste:粘贴
//  Cut:剪切
Event.current.delta//鼠标间隔移动距离
Event.current.functionKey//是否是功能键输入 功能键指小键盘中的 方向键, page up, page down, backspace等等
Event.current.numeric//小键盘是否开启
Event.current.Use()//避免组合键冲突
//  在处理完对应输入事件后，调用该方法，可以阻止事件继续派发，放置和Unity其他编辑器事件逻辑冲突
```

