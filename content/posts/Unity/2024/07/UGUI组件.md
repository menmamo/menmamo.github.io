---
title: "UGUI组件"
author: "mona"
date: 2024-07-08T17:35:00+08:00
categories: ["Unity"]
tags: ["UGUI"]
---

## Image组件

Image是图像组件

是UGUI中用于显示精灵图片的关键组件

是UGUI中用于显示精灵图片的关键组件

### Image组件参数

- **Source Image**

  图片来源

  必须是精灵类型

- **Color**

  图像的颜色

- **Material**

  图像的材质 一般不会修改 使用默认的

- **Raycast Target**

  是否作为射线检测的目标 如果不勾选则不会响应射线检测

- **Maskable**

  是否能被遮挡

- **Image Type**

  图片类型

  - Simple 扑通模式 缩放整个图片
  - Sliced 9宫格拉伸
  - Tiled 平铺模式
  - Filled填充模式 
    1. Fill Method 填充方式
    2. Fill Orgin 填充原点
    3. Fill Amount 填充量
    4. Clockwise 顺时针方向

- **Use Sprite Mesh**

  使用精灵网络，勾选会生成图片网络

- **Preserve Aspect** 

  保持图片比例

- **SetNative Size** 

  设置为图片资源的原始大小

### Image代码相关

```c#
//获取图片组件
Image img = this.GetComponent<Image>();
//设置图片
img.sprite = Resources.Load<Sprite>("ui_TY_fanhui_01");
//设置图片大小
(transform as RectTransform).sizeDelta = new Vector2(200, 200);
//设置图片射线检测
img.raycastTarget = false;
//设置图片颜色
img.color = Color.red;
```

## Text文本组件

Text是文本组件

是UGUI中用于显示文本的关键组件

### Text参数相关

- **Text**

  文本显示内容

- **Font** 

  字体

- **FontStyle**

  字体样式

  - Normal普通
  - Blod加粗
  - ltalic 斜体
  - Blod And Italic 加粗+斜体

- **Font Size** 

  字体大小

- **Line Spacing** 

  行之间的垂直间距

- **Rich Text**

  是否开启富文本

- **Alignment** 

  对齐方式

- **Align By Geometry** 

  使用字形集合形状范围进行水平对齐 而不是字形指标

- **Horizontal Overflow** 

  处理文本太宽放入矩形范围内的方式

  - Warp 包裹模式 字体始终在矩形范围内 会自动换行
  - Overflow 溢出模式 字体可以溢出矩形框

- **Vertical Overflow**  

  处理文本太高放入矩形范围内的方式

  - Truncate 截断模式 字体始终在矩形范围内 超出部分不会显示
  - Overflow 溢出模式 字体可以溢出矩形框

- **Best Fit** 

  忽略字体大小 始终把内容完全显示在矩形框中，会自动调整文字大小

  - MinSize 最小多小
  - MaxSize 最大多大

### Text组件代码相关

```c#
Text txt = this.GetComponent<Text>();
txt.text = "哈哈哈哈哈";
```

## RawImage

RawImage是原始图像组件

是UGUI中用于显示任何纹理图片的关键组件

它和Image的区别是 一般RawImage用于显示大图(背景图，不需要打入图集的图片，网络下载的图等等)

### RawIamge参数

- **Texture**

  图像纹理

- **UV Rect**

  图像在UI矩形内的偏移和大小

  位置偏移的XY

  大小偏移的W和H

  改变他们图像边缘将会进行拉伸来填充UV矩形周围的空间

### RawIamge代码相关

```c#
RawImage raw = this.GetComponent<RawImage>();
raw.texture = Resources.Load<Texture>("ui_TY_lvseshuzi_08");
raw.uvRect = new Rect(0, 0, 1, 1);
```

## Button 按钮

Button是按钮组件

是UGUI中用于处理玩家按钮相关交互的关键组件

默认创建的Button由2个对象组成

父对象——Button组件依附对象 同时挂载了一个Image组件 作为按钮背景图

子对象——按钮文本（可选）

### Button 按钮参数

- Interactable 

  响应用户输入的过过渡效果

  - None 没有过渡效果
  - ColorTint 用颜色表示不同状态的变化
  - Sprite Swap 用图片表示不同状态的变化
  - Animation 用动画表示不同状态的变化

- Navigation

  导航模式 可以设置UI元素如何在播放模式中控制导航

  - None 无键盘导航
  - Horizontal水平导航
  - Verticval 垂直导航
  - Automatic 自动导航
  - Explicit 指定周边控件进行导航

- OnClick

  单机执行的函数列表

### 过渡效果-ColorTint 

- TargetGraphic 控制的目标图形
- Normal Color正常状态下的颜色
- Highlighted Color 鼠标进入时候显示的颜色
- Pressed Color 鼠标按下的颜色
- Selected Color 选中的颜色
- Disable Color 禁用时的颜色
- Color Multiplier：颜色倍增器 过渡时颜色乘以该值
- FadeDuration 衰减持续事件 从一个状态刀另一个状态需要的时间

### 过渡效果-Sprite Swap

- Highlighted Sprite 鼠标进入时候显示的图片
- Pressed Sprite 鼠标按下的图片
- Selected Sprite 选中的图片
- Disable Sprite 禁用时的图片

### 过渡效果-Animation 

- Normal Trigger正常状态下的动画触发器
- Highlighted Trigger 鼠标进入时触发器
- Pressed Trigger鼠标按下的触发器
- Selected Trigger选中的触发器
- Disable Trigger禁用时的触发器

## Button代码相关

**获得按钮组件设置参数**

```c#
Button btn = this.GetComponent<Button>();
btn.interactable = true;
btn.transition = Selectable.Transition.None;

Image img = this.GetComponent<Image>();
```

**添加或删除事件**

```c#
btn.onClick.AddListener(ClickBtn2);
btn.onClick.AddListener(() => {
    print("123123123");
});

btn.onClick.RemoveListener(ClickBtn2);
btn.onClick.RemoveAllListeners();
```

## Toggle开关

Toggle是开关组件

是UGUI中用于处理玩家单选框多选框相关交互的关键组件

可以通过配合ToggleGroup组件制作为单选框

默认创建的Toggle由4个对象组成

父对象——Toggle组件依附

子对象——背景图（必备）、选中图（必备）、说明文字（可选）

### Toggle 参数

- **Interactable** 

  是否接受输入

- **Transition** 

  响应用户输入的过渡效果

- **Navigation**

  导航模式 可以设置UI元素如何在播放模式中控制导航

- **IsOn** 

  当前状态

- **Toggle Transition** 

  在开关值发生变化时的过渡方式

  - None 无过渡
  - Fade 淡入淡出

- **Graphic** 

  用于表示选中状态的图片

- **Group**

  单选框分组

- **OnValue Changed** 

  开关状态变化时执行的函数

### ToggleGroup 开关分组

Allow Switch Off 是否允许补选中任何一个单选框

可以挂载到任何对象上 只要单选框的**Group**参数与其关联

### Toggle代码控制

```c#
Toggle tog = this.GetComponent<Toggle>();
tog.isOn = true;
print(tog.isOn);

ToggleGroup togGroup = this.GetComponent<ToggleGroup>();
togGroup.allowSwitchOff = false;

//可以遍历提供的迭代器 得到当前处于选中状态的 Toggle
foreach (Toggle item in togGroup.ActiveToggles())
{
    print(item.name + " " + item.isOn);
}
tog.onValueChanged.AddListener(ChangeValue2);
tog.onValueChanged.AddListener((b) =>
{
    print("代码监听 状态改变" + b);
});
```

## InputField输入

InputField是输入字段组件

是UGUI中用于处理玩家文本输入相关交互的关键组件

默认创建的InputField由3个对象组成

父对象——InputField组件依附对象 以及 同时在其上挂载了一个Image作为背景图

子对象——文本显示组件（必备）、默认显示文本组件（必备）

### InputField参数

- **Interactable** 

  是否接受输入

- Transition

  响应用户输入的过渡效果

- Navigation 

  导航模式 可以设置UI元素如何在播放模式中控制器导航

- TextComponent

  关联输入内容的文本组件

- Text

  输入框的初始值

- Character Limit

  可以输入字符长度的最大值

- Content Type

  输入的字符类型限制

  	- Standard 标准模式 可以输入任何字符
  	- Autocorrected 自动更正模式 搜索位置单词，向用户建议适合的替换候选词
  	- Integer Number 整数模式
  	- Decimal Number 十进制模式 只能输入数字包括小数
  	- Alphanumeric 字母数字模式
  	- Name 名字模式
  	- Email Address 邮箱模式
  	- Password 密码模式
  	- Pin 别针模式 用星号隐藏输入字符 只可以输入整数
  	- Custom 自定义模式

- Line Type

  行类型 定义文本格式

  - Single Line 只允许单行显示
  - Multi Line Submit 允许使用多行 尽在需要适使用新的一行
  - Multi Line NewLine 允许使用多行 用户可以按回车换行

- Placeholder

  关联用于显示初始内容的文本控件

- Caret Blink Rate 

  光标闪烁速率

- Caret Width 

  光标宽

- Custom Caret Color 

  自定义光标颜色

- Selection Color

  批量选中的背景颜色

- Hide Mobile Input

  隐藏移动设备上的键盘 仅适用于ios

- Read Only

  只读 不可以修改

- On Value Changed 

  内容改变时执行的方法

- On End Edit 

  结束输入时执行的方法

### InputField代码相关

```c#
InputField input = this.GetComponent<InputField>();
print(input.text);
input.text = "123123123123";
//监听事件的两种方式
//1.拖脚本
//2.代码添加
input.onValueChanged.AddListener((str) =>
{
    print("代码监听 改变" + str);
});

input.onEndEdit.AddListener((str) =>
{
    print("代码监听 结束输入" + str);
});
```

## Slider滑动条

Slider是滑动条组件是UGUI中用于处理滑动条相关交互的关键组件

默认创建的Slider由4组对象组成

父对象——Slider组件依附的对象

子对象——背景图、进度图、滑动块三组对象

### Slider参数

- **FillRect**

  填充进度条图形

- **Hadle Rect** 

  滑动块图形

- **Direction** 

  滑动条的方向

  - Left To Right 从左到右
  - Right To Left 从右到左
  - Bottom To Top从下到上
  - Top To Bottom 从上到下

- **Min Value** 和 **Max Value**

  最大值和最小值

- **Whole Numbers** 

  是否约束为整数值变化

- **Value** 

  当前滑动条的数值

- **OnValueChanged** 

  滑动条值改变时执行的函数

  静态的自己传入值执行

  动态的会自动传入float类型的value值

### Silder代码相关

```c#
Slider s = this.GetComponent<Slider>();
print(s.value);
 s.onValueChanged.AddListener((v) =>
{
    print("代码添加的监听" + v);
});
```

## Scrollbar滚动条

Scrollbar是滚动条组件

是UGUI中用于处理滚动条相关交互的关键组件

默认创建的Scrollbar由2组对象组成

父对象——Scrollbar组件依附的对象

子对象——滚动块对象

一般情况下我们不会单独使用滚动条 

都是配合ScrollView滚动视图来使用

###  Scrollbar参数

- Interactable 是否接受输入
- Transition 响应用户输入的过渡效果
- Navigation 导航模式
- Hadle Rect 关联滚动块图形的对象
- Direction 滚动条的方向
- Value 滚动条初始位置值
- Value 滚动条在条中的比例大小
- Number Of Steps 允许可以滚动多少次（不同滚动位置的数量）
- OnValueChanged 滚动条值改变时执行的函数列表

## Scroll view滚动视图

默认创建的Scroll view由4组对象组成

父对象——ScrollRect组件依附的对象 还有一个Image组件 最为背景图

子对象
Viewport控制滚动视图可视范围和内容显示
Scrollbar Horizontal 水平滚动条
Scrollbar Vertical 垂直滚动条

###  ScrollRect参数

- **Content**

  控制滚动视图显示内容的父对象，他的尺寸有多大滚动视图就能拖多远

- **Horizontal**

  启用水平滚动

- **Vertical**

  启用垂直滚动

- **Movement Type**

  滚动视图元素的运动类型。主要控制拖动时的反馈效果

  - Unrestricted 不受限制 一般不使用
  - Elatic 常用 回弹效果 当滚出边缘后 会弹回边界
  - Elasticity 回弹系数
  - Clamped 夹紧效果 没有回弹效果 始终限制在范围内

- **Inetia** 

  惯性移动 如果开启 松开鼠标后会有一定的惯性

  - Deceleration Rate 减速率 0没有惯性 1不会停止

- **Scroll Sensitivity**

  滚轮和触摸板的滚动事件敏感性

- **Viewport** 

  关联视图内容视口对象

- **Horizontal Scrollbar** 

  关联水平滚动条

- **Visibility** 

  是否在不需要时自动隐藏滚动条

  - Rermanent 始终显示
  - Auto Hide 自动隐藏
  - Auto Hide And Expand Viewport 自动隐藏滚动条 并且 自动扩展内容视口

- **Spacing** 

  滚动条和视口之间的间隔控件

- **OnValueChanged** 

  滚动视图位置改变时执行的函数列表

### Scroll View 代码相关

```c#
ScrollRect sr = this.GetComponent<ScrollRect>();
//改变内容的大小 具体可以拖动多少 都是根据它的尺寸来的
//sr.content.sizeDelta = new Vector2(200, 200);

sr.normalizedPosition = new Vector2(0, 0.5f);
#endregion

#region 知识点四 监听事件的两种方式
//1.拖脚本
//2.代码添加

sr.onValueChanged.AddListener((vec) =>
{
    print(vec);
});
```

## DropDown下拉列表

默认创建的DropDown由4组对象组成

父对象

DropDown组件依附的对象 还有一个Image组件 作为背景图

子对象

Label是当前选项描述

Arrow右侧小箭头

Template下拉列表选单

### DropDown参数

- **Interactable**

  是否接受输入

- **Transition**

  响应用户输入的过渡效果

- **Navigation**

  导航模式

- **Template**

  关联下拉列表对象

- **Caption Text**

  关联显示当前选择内容的文本组件

- **Caption Image**

  关联显示当前选择内容的图片组件

- **Item Text**

  关联下拉列表选项用的文本控件

- **Item Image**

  关联下拉列表选项的图片控件

- **Value**

  当前所选项的索引值

- **Alpha Fada Speed**

  下拉列表窗口淡入淡出的速度

- **Options**

  存在的选项列表

### DropDown 代码相关

```c#
Dropdown dd = this.GetComponent<Dropdown>();
print(dd.value);
print(dd.options[dd.value].text);
dd.options.Add(new Dropdown.OptionData("123123123"));
dd.onValueChanged.AddListener((index) => {

    print(index);
});
```

