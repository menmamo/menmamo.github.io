---
title: "UI系统-NGUI"
author: "mona"
date: 2024-06-17T11:48:00+08:00
categories: ["Unity"]
tags: ["NGUI"]
---

## NGUI

### 创建NGUI

- Project面板左键 或者在导航栏 选择NGUI
- 选择Create 2D UI

### UI Root

#### Scaling Style 分辨率自适应模式

- Flexible 灵活模式

  - 在该模式下 UI都是以像素为基础，100像素的物体无论在多少分辨率上都是100像素
  - 这就意味着 100像素在分辨率低的屏幕上可能显示正常 但是在高分辨率的屏幕上就很小

- Flexible模式的配置参数

  - Minimum Height 屏幕高度小于该值时 开始按比例缩放
  - Maximum Height 屏幕高度 大于该值时 开始按比例缩放
  - Shrink Portrait UI 竖屏时 按宽度来适配
  - Adjust By Dpi 使用dpi做计算 建议勾选

- Constrained 约束模式

  - 在该模式下 屏幕按尺寸比例来适配 不管实际屏幕有多大
  - NGUI会通过合适的缩放来适配屏幕
  - 好处是在各个设备上看到的ui和比例是一样的 但是有可能会因为缩放显示模糊

- Constrained 参数

  - Content Width 按照该宽度值适配屏幕（制作资源时的默认分辨率宽）
  - Content Height 按照该高度值适配屏幕（制作资源时的默认分辨率高）
  - Fit表示以哪个值来做适配
    - Width：UI会横向填充屏幕 
    - Height：UI会纵向填充屏幕
    - 两个都勾选 不会被裁剪 但是会有黑边 当宽高比大于实际宽高比时 按宽度适配 反之按高度适配
    - 两个都不勾选 始终保证屏幕被UI填充满 不会有黑边 但内容可能会被裁剪

- Constrained On Mobile 混合模式

  - PC使用灵活模式 移动端使用约束模式


### Panel

- 负责管理一个UI面板上所有子控件的渲染
- UIRoot中也有一个Panel
- 在老版本NGUI中叫做UI Panel

#### Panel参数

- Alpha 控制所有子控件的透明度
- Depth 控制该Panel的渲染优先级 层级高的后渲染 会把先渲染的挡住
- Clipping 裁剪
  - None 不处理 默认
  - Texture Mask 根据图片信息进行遮罩
  - Soft Clip 自己定义裁剪范围
  - Constrain But Dnot Clip 约束但不裁剪 不裁剪画面 值影响相应范围
- Sorting Layer排序层级

#### Advance Options 不常用

- Advanced Options 渲染相关高级选项
  - Render 渲染队列
  - Sort Order 排序
  - Normals是否需要灯光着色器
  - UV2 是否用于自定义着色器效果
  - Shadow Mode
  - Cull 元素插件拖动时提出
  - Visible 检查元素组件是否离开屏幕
  - Padding 边界内容
  - Offset 抵消偏移位置
  - Static 检查子元素是否会移动
  - Panel Tool 是否显示面板工具
- Anchors锚点设置 用于分辨率自适应设置大小

### EventSystem

- 主要作用是让摄像机渲染出来的物体能够接收到Ngui的输入事件 大部分设置不需要我们主动修改
- 有了它我们可以通过鼠标 键盘 控制器等等 操作ui 相应玩家的输入
- 在老版本的Ngui中叫做UI Camera

#### EventSystem参数

- EventType 事件类型 决定了脚本如何对鼠标和触屏事件进行相应
  - UI模式 会根据UI组件的深度来检测
  - World模式 根据距离主摄像机的远近 进行检测
  - 2D和3D的区别 碰撞盒是用3D的还是2D的区别
- Events go to 事件通过刚体还是碰撞盒来传递
- Process Events In 事件的更新是在Update还是LateUpdate中 一般不会修改
- Event Mask 决定哪个游戏对象层级会接受事件
- Debug 是否开启调试模式
- Command Click 苹果电脑上是否启用Command按键模拟右键操作
- Allow Multi Touch 是否支持多点触控
- Auto Hide Cursor 当游戏又控制器或者其他输入设备时是否自动隐藏光标
- Sticky Tooltip是否使用tooltip
- Long Press Tooltip 是否长按出提示
- Tooltip Delay 停留多久出现tip
- Raycast Range 射线长度 一般不修改
- EventSources 接收的事件来源  键盘 鼠标 等等
- Thresholds调整鼠标事件的 点击 拖 轻拍等行为
- Axes and key 热键关系 一般不修改

### 图集

- NGUI中的最小图片控件Sprite要使用图集中的图片进行显示
- 图集 就是把很多单独的小图 合并为一张大图
- 目的是为了提高渲染性能

#### 打开图集制作器

- 方法一 在Project右键打开
- 方法二 导航栏Ngui - Open - Atlas Maker

#### 图集关键文件

- 图集文件
- 图集材质
- 图集图片

#### 图集工具参数

- Padding 每个图片的像素间隔
- Trim Alpha 移除图片多余的空白控件（抠图）
- PMA Shader 预乘透明通道
- Unity Packer 自定义打包器
- Truecolor 强制ARGB32纹理
- Auto-upgrade 自动更新 用精灵替换纹理
- Force Square 如果启用强制 生成后的图集长款都为2的n次方（部分老的ios设备只支持正方形图集）
- Pre-processor 预处理器

### Sprite 精灵

#### 参数

- Atlas图集
- Sprite 图集中的精灵图片
- Material 材质 一般不会修改
- Fixed Aspect 是否固定宽高比
- Type 图片类型
  - Simple 普通模式
  - Sliced 九宫格切片
  - Tiled 平铺模式 图片会重复绘制
  - Filded填充模式 可以做CD 进度条等 
  - Advanced 高级模式 可以把图片分成五个部分 分别设置模式
- Flip翻转模式
  - 水平翻转
  - 垂直翻转
  - 水平垂直翻转
- Gradient 渐变色  勾选后上部颜色和下部颜色自动渐变
- Color Tint 颜色叠加

### 公共内容 Widget

- Pivot控制对象锚点
- Depth深度层级 决定渲染顺序
- Size 尺寸
- Aspect 宽高比

### Label

- Font 字体 使用Untiy字体可以使用fft字体文件 也可使用NGUI字体图集
- Material材质 一般不会修改
- FontSize 字体大小  
- Text 显示内容
- Modifier 修饰方式
  - None 没有限制
  - To Uppercase 大写字母
  - To Lowercase 小写字母
  - Custom 自定义
- Overflow
  - Shirnk Content 收缩内容
  - Clamp Content 夹紧内容
  - Resize Freely自动调整大小
  - Resize Height 自动调整高
    - Label控件高度会无法手动调节 会随着字体内容大小会自动改变
  - Alignment 字体的对其方式
- Keep crispp 动态字体锐化
  - Never从不
  - On Desktop和Always 区别不大都会开启
- Gradient 渐变
- Effect特效
  - None没有特效
  - Shadow 阴影效果
  - OutLine 边缘线
- Float spacing 开启浮点数间隔调整 默认整形
- Spacing 字符间隔
- Max Lines 最大行数
- BBCode是否开启富文本
- Color Tint 字体颜色 

### 富文本

```
[ff000]颜色
[b]加粗[/b]
[i]斜体[/i]
[u]下划线[/u]
[s]删除线[/s]
[sub]下角标[/sub]
[sup]上角标[/sup]
[url = http://baidu.com/]超链接[/url]
```

如果希望点击超链接可以打开页面 需要添加碰撞体 和脚本Open URL On Click

### Texture 大图控件

- Sprite 只能显示图集中的图片 一般用于小图片
- 大尺寸的图片 没有必要打成图集 所以使用Texture显示
- Testure参数 基本上和Sprite 控件一样

### NGUI组合控件 按钮

- 在三个基础组件对象上 添加对应组件
- 如果希望响应点击事件 需要添加碰撞器

### 制作Button

- 一个Sprite
- 为Sprite添加Button脚本
- 添加碰撞器

### Button参数

- Twen Target 按钮控制的目标 一般是三个基础控件之一 会自动设置
- DragOver 一般不会使用
- Transition 颜色渐变的时间
- Colors 按钮各个状态的颜色设置
- Sprites 按钮各状态的图片设置
- OnClick 点击按钮响应脚本

### Button监听事件的两种方式

- 拖脚本
- 代码获取按钮添加监听

```c#
btn.onClick.add(new EventDelegate(ClickDoSomthing));
public void ClickDoSomthing(){
	print("按钮被点击");
}
```

## Toggle单选框多选框

### 创建Toggle

为Sprite添加Toggle和碰撞器脚本 然后绑定一个Sprite

### Toggle参数

- Group分组 单个多选框分为一组 变成单选框
  - State of None 单选框状态时 是否允许不选中
- Starting State 开始默认状态 默认为选中
- Sprite 选中用的图片
- Invert State 反装显示  如果选中不显示 不选中显示
- Animator 状态变化时播放动画 新动画系统
- Animation  状态变化时播放动画 老动画系统
- Tween 状态变化时缓动
- Transition 过渡模式
- OnValueChange 状态变化时响应脚本

### 获取Toggle状态

```c#
toggle.value;
```



## 输入框

### 创建输入框

为Sprite添加Input和碰撞器脚本并绑定一个Lable组件

### 输入框参数

- Label关联的文本组件
- Starting Value 开始默认显示的内容
- Saved As 会使用PlayerPrefs将输入内容保存 这里填写key 一般不用
- ActiveTextColor 选中激活时的颜色
- Inactive Color 未选中失活时的颜色
- Caret Color 插入的光标颜色
- Selection Color 选中文字的背景颜色
- Input Type输入类型
  - Standard默认模式
  - Auto Correct自动更正
  - Password 密码输入
- Vaildation输入限制
  - None 无限制
  - Integer只能输入整形
  - Float 浮点数
  - Alphanumeric 只能是数字或者字母
  - Username 用户名
  - Name 姓名
  - Filename 文件名
- MoblieKeyboard 手机键盘模式
  - Default 英文呢
  - ASCII Capable 数字符号
  - Number And Puntuation 数字和符号
  - URL 链接
  - Number Pad 数字
  - Phone Pad 手机
  - Name Phone Pad 名字
  - Email Address  邮箱地址
- Hide Input 键盘下 隐藏输入框
- On Return Key 回车键 操作
- Character Limit 最大可输入字符数
- OnSubmit 输入完成时相应脚本
- OnChange 输入变化时响应脚本

## 下拉列表

### 创建下拉列表

创建一个Sprite和一个Label 在Sprite添加碰撞体和Popup List

### 下拉列表参数

- Options 下拉列表显示内容 每一行表示一个内容
- Position 列表出现位置
  - Auto 自动
  - Above 向上
  - Below向下
- Selection选中操作
  - On Press 按下选中
  - On Click点击选中
- Alignment对齐方式
  - Automatic自动对其
  - Left 左对齐
  - Center剧中对其
  - Right右对齐
  - Justifired 调整会自动变化
- Open on 列表打开方式
  - 点击或触碰
  - 右键
  - 双击
  - 手动（相当于关闭 需要用代码处理）
- On Top始终显示在所有面板之前 默认选择
- Localized 是否对弹出列表的值进行本地化
- Keep Value 始终保持有列表中的某个默认值
  - 将值显示在lable上
  - 需要将lable拖到on vlue change中 调用lable的SetCurrentSelection方法
- Atlas图集
  - Background修改下拉列表的背景图
  - Highlight 修改下拉列表选中的背景
  - Background 背景颜色叠加
  - Highlight 选中高亮叠加颜色
  - Overlap 弹出窗口边框与打开它的内容重叠的数量
  - Animated 是否有默认的弹出动画 禁用可以节约性能
- Font 字体
  - Font 字体
  - Font Size 字体大小
  - TextColor 字体颜色
  - Padding 便宜位置

## Silder 滑动条

### 创建滑动条

需要创建3个Sprite 并在背景Sprite 上添加slider组件和碰撞器

### 滑动条参数

- Value 当前值
- Steps 步数 将滑动条平分多少份
- Appearace 外观设置
- Foreground 前景用于缩放
- Background 背景
- Thumb 拖动快
- Direction 拖动方向
  - Left To Right 从左到右
  - Right to Left 从右到左
  - Bottom To Top 从下到上
  - Top to Buttom 从上到下
- OnValueChange 值变化时监听的脚本

## Scrollbar滚动条

### 创建Scrollbar

- 创建两个Sprite 一个背景和一个滚动条
- 背景作为父对象 并添加碰撞器和Scrollbar组件

### Scrollbar参数

和Silder基本一致

## ProgressBar进度条

### 创建ProgressBar

- 创建两个Sprite 一个背景 一个进度条
- 背景作父对象 添加ProgerssBar组件

## ScrollView滚动视图

### 创建滚动视图

- NGUI-Create-ScrollView
- 若需要Scrloobar 需要自行添加
- 添加子对象 子对象需要添加Drag Scroll View 和碰撞器

### 滚动视图参数

- Content Origin 内容子对象对齐方式
- Movement拖曳方向
  - Horizontal 水平
  - Veritcal垂直
  - Unrestricted自由
  - Custom自定义
- Drag Effect拖动特效
  - None 不适用任何效果
  - Momentum 惯性效果
  - Momentum And Spring 弹性效果和弹力效果
- Scroll Whell Factor 滚动因子 如果不为0 鼠标滚轮可以滚动它 控制鼠标滚轮的速度和方向
- Momentum Amount 动量 拖拽一下鼠标 动的快慢 可以理解为是惯性的大小
- Sppring Strenth 弹力大小
- Dampen Strength 阻尼强度 影响回弹效果
- Restrice Within Panel 将内容限制在Panel 中不勾选不会产生弹力效果
- Constrain On Drage 阻力约束 一般不会修改
- Cancel Drag if fits 若勾选 滚动视图的内容是否应该是可动的 取决于他们当前的子对象是否溢出
- Smooth Drag Start 平滑拖动 一般不修改
- IOS Drag Emulation ios阻力模拟 一般不修改
- SrollBars 滚动条关联
  - Horizontal 水平滚动条
  - Veritcal 垂直滚动条
  - ShowConditon 显示时机
    - Always 一直显示
    - Only if Needed  需要时显示
    - WhenDragging 拖动时显示

### Drag Scroll View

- 添加Drag ScrollView 即可在滚动视图中被拖动
- 参数可以不传 运行时会自动获取到

### Grid表格布局

- Arrangement 排序对齐方式 水平 垂直 元素大小
- Cell width 元素框
- Cell Height 元素高
- Row Limit 元素个数  设置后超过设置的数量会自动换行
- Sorting 排序顺序
  - None 没有排序
  - Alohabetic按字母排序
  - Horizotal水平放置顺序
  - Vertical 垂直放置顺序
  - Custom 自定义
- Inverted倒转 如果选择了排序方式 勾选这里可以翻转排序规则
- Pivot 锚点位置
- Smooth Tween 平缓缓动动画 是否会平滑的将子对象设置为正确的位置
- Hide Inactive 是否隐藏不活动的组件
- Constrain To Panel 约束面板 是否将网络子对象的更改通知父容器Panel 用于更新Scrollbar等信息

## Anchor锚点组件

- 老版本——锚点组件——用于控制对象对齐方式
- 新版本——3大基础控件自带 锚点内容——用于控制对象相对父对象布局

### 老版本——锚点组件

- UICamera 关联UI摄像机
- Container控制的容器对象
- Side 九宫格位置
- Run Only Once 是否只对其一次
- Relative Offset 相对比例位置偏移
- Pixel Offset 像素偏移位置

### 新版本——基础控件自带锚点信息

- Type 尺寸对齐方式
  - None 无
  - Unified 统一模式
  - Advance 高级模式
- Execute什么时候执行更新
- Target 相对目标

## EventListener和EventTrigger

- NGUI提供了一些利用反射调用的特殊响应函数
- 经过 OnHover 按下 OnPress等等 可以直接写在脚本中 挂载到ui对象中
- NGUI会自动调用
- UIEventListener和UIEventTrigger封装了所有的特殊响应函数

### UIEventListener

```c#
//如果这个对象没有挂载UIEventListener 则会添加一个并返回
UIEventListener listener = UIEventListener.Get(A.gameObject);
listener.onPress += (obj, isPress) => {
    print(obj.name + "被按下或者抬起了" + isPress);
};
```

### UIEventTrigger

- UIEventTrigger 适合Inspector面板 关联脚本添加
- UIEventTrigger中的方法没有参数 但是可以在Inpector面板中 通过拖拽的方式添加委托函数

## DrawCall

- DrawCall就是CPU(处理器)准备好渲染数据（顶点，纹理，法线，Shader等等）后告知GPU(图形处理器-显卡)开始渲染（将命令放入命令缓冲区）的命令
- 一次DrawCall就是 CPU准备好渲染数据通知 GPU渲染的这个过程
- 假设我们创建10000个小文件，每个文件大小为1kb，然后把这些文件拷贝到另一个文件夹中你会发现，即使这些文件加起来不超过10MB，但是拷贝花费的时间是很长的如果我们单独创建1个10MB的文件拷贝到另一个文件夹，基本可以瞬间拷贝完毕

### 如何降低DrawCall

在UI层面上 小图合大图——>即多个小DrawCall变一次大DrawCall

制作图集，可以有效的减低Drawcall，但不同图集之间会打断Drawcall的合并。
假设有A、B、C 三张图集。
节点结构1： AAABBBCCC，这样的排序下会产生3个Drawcall；
节点结构2： ABCABCABC，这样的排序下会产生9个Drawcall；
那么我们在制作过程中节点的排序需要尽量保证相同材质按照 结构1 紧挨着渲染，避免交叉渲染导致

制作UI时降低DrawCall的技巧

1. 通过NGUI Panel上提供的DrawCall查看工具
2. 注意不同图集之间的层级关系
3. 注意Label的层级关系

## NGUI字体

- NGUI内部提供了字体制作工具
- 可以根据字体文件 生成指定内容文字的图集 达到降低DrawCall的目的
- 还可以导入 外部第三方工具(BitmapFont)生成的字体图集
  - 通过第三方工具 可以自定义美术字体 然后导入到ngui的字体制作工具中

### 如何选择Unity动态字体 和NGUI字体

1. 文字变化较多用Unity动态字体 变化较少用NGUI字体
2. 想要减少DrawCall用NGUI字体 
3. 美术字用NGUI字体

## NGUI缓动效果

给UI组件添加Tween组件 就可以实现缓动效果

### 参数

- From开始状态
- To 结束状态
- PlayStyle播放方式
  - Once 一次
  - Loop循环
  - PingPong 从头播放到尾 再从尾播到头
- Animation Curve 动画取消 可以调整两个值的曲线变化
- Duration 持续时间
- Start Delay 开始播放前的延迟时间
- Delay Affects 延迟影响
- TweenGroup 分组ID 用于一个对象多个动画时分组
- Ignore TimeScale 忽略TimeScale
- Use Fiexd Update 使用物理帧更新动画

### Play Tween控制缓动效果播放

- 给需要控制缓动效果的ui组件添加Play Tween组件
- Play Tween可以添加多个 通过不同的事件来触发

### 参数

- Tween Target 控制对象  可以自动关联 也可以手动指定
- Include Children 是否带着子对象一起变化
- Start State 如果勾选 则在触发激活前 PlayTween将在启动时将所有关联的Tween重置为起始状态
  - 可以理解为 默认不播放缓动效果 通过事件去控制它
- TweenGroup 控制是那一组缓动
  - 对应Tween Scale的分组id
- Trigger Conditon 触发条件
- Play Direction 播放的方向
  - Reverse翻转播放 
    - 一开始就是结束状态 往回播放
  - Toggle正反状态转换
    - 每次播放 变换方向
  - Forward正向播放
- If target is disabled 如果控制的对象失活 处理方式
  - Do Nothing 什么也不做
  - Enable Then Play 播放时激活它
  - Ignore Disabled State 忽略失活状态
- On activation 激活时
  - Continue From Current 继续播放
  - Restart Tween重新开始
  - Restart if Not Playing 如果没有播放重新开始
- When finished 播放完毕做什么
  - Disable After Reverse 执行完后隐藏
  - Do Not Disable 什么也不做
  - Disable After Forward 如果是倒着播 播完后隐藏

## NGUI中显示3D模型

### 方法一：

- 使用UI摄像机渲染3D模型
- 改变NGUI的整体层级 为 UI层
- 改变主摄像机和NGUI摄像机的 渲染层级
- 将想要被UI摄像机渲染的对象层级改为 UI层
- 调整模型和UI控件的Z轴距离

### 方法二：

- 使用多摄像机渲染 Render Texture
- 让Panel和粒子特效处于一个排序层
- 在粒子特效的 Render参数中 设置自己的层级

## NGUI不常用组件

### PlaySound 脚本

可以根据事件类型 播放音效

### KeyBinding 脚本

和键盘案件绑定触发按钮等

### KeyNavigation 脚本

可以通过tab 上下左右按键等 快捷的选中按钮

### 语言本地化

- 在Resources下创建一个txt文件 命名必须为Localization
- 配置文件
- 给想要切换文字的Label对象下挂载Localize 关联Key
- 给用于切换语言的下拉列表下添加脚本LanguageSelection

## NGUI中显示3D模型

### 方法一：

- 使用UI摄像机渲染3D模型
- 改变NGUI的整体层级 为 UI层
- 改变主摄像机和NGUI摄像机的 渲染层级
- 将想要被UI摄像机渲染的对象层级改为 UI层
- 调整模型和UI控件的Z轴距离

### 方法二：

- 使用多摄像机渲染 Render Texture
- 让Panel和粒子特效处于一个排序层
- 在粒子特效的 Render参数中 设置自己的层级

## NGUI不常用组件

### PlaySound 脚本

可以根据事件类型 播放音效

### KeyBinding 脚本

和键盘案件绑定触发按钮等

### KeyNavigation 脚本

可以通过tab 上下左右按键等 快捷的选中按钮

### 语言本地化

- 在Resources下创建一个txt文件 命名必须为Localization
- 配置文件
- 给想要切换文字的Label对象下挂载Localize 关联Key
- 给用于切换语言的下拉列表下添加脚本LanguageSelection

## NGUI中显示3D模型

### 方法一：

- 使用UI摄像机渲染3D模型
- 改变NGUI的整体层级 为 UI层
- 改变主摄像机和NGUI摄像机的 渲染层级
- 将想要被UI摄像机渲染的对象层级改为 UI层
- 调整模型和UI控件的Z轴距离

### 方法二：

- 使用多摄像机渲染 Render Texture
- 让Panel和粒子特效处于一个排序层
- 在粒子特效的 Render参数中 设置自己的层级

## NGUI不常用组件

### PlaySound 脚本

可以根据事件类型 播放音效

### KeyBinding 脚本

和键盘案件绑定触发按钮等

### KeyNavigation 脚本

可以通过tab 上下左右按键等 快捷的选中按钮

### 语言本地化

- 在Resources下创建一个txt文件 命名必须为Localization
- 配置文件
- 给想要切换文字的Label对象下挂载Localize 关联Key
- 给用于切换语言的下拉列表下添加脚本LanguageSelection

## NGUI中显示3D模型

### 方法一：

- 使用UI摄像机渲染3D模型
- 改变NGUI的整体层级 为 UI层
- 改变主摄像机和NGUI摄像机的 渲染层级
- 将想要被UI摄像机渲染的对象层级改为 UI层
- 调整模型和UI控件的Z轴距离

### 方法二：

- 使用多摄像机渲染 Render Texture
- 让Panel和粒子特效处于一个排序层
- 在粒子特效的 Render参数中 设置自己的层级

## NGUI不常用组件

### PlaySound 脚本

可以根据事件类型 播放音效

### KeyBinding 脚本

和键盘案件绑定触发按钮等

### KeyNavigation 脚本

可以通过tab 上下左右按键等 快捷的选中按钮

### 语言本地化

- 在Resources下创建一个txt文件 命名必须为Localization
- 配置文件
- 给想要切换文字的Label对象下挂载Localize 关联Key
- 给用于切换语言的下拉列表下添加脚本LanguageSelection

## NGUI中显示3D模型

### 方法一：

- 使用UI摄像机渲染3D模型
- 改变NGUI的整体层级 为 UI层
- 改变主摄像机和NGUI摄像机的 渲染层级
- 将想要被UI摄像机渲染的对象层级改为 UI层
- 调整模型和UI控件的Z轴距离

### 方法二：

- 使用多摄像机渲染 Render Texture
- 让Panel和粒子特效处于一个排序层
- 在粒子特效的 Render参数中 设置自己的层级

## NGUI不常用组件

### PlaySound 脚本

可以根据事件类型 播放音效

### KeyBinding 脚本

和键盘案件绑定触发按钮等

### KeyNavigation 脚本

可以通过tab 上下左右按键等 快捷的选中按钮

### 语言本地化

- 在Resources下创建一个txt文件 命名必须为Localization
- 配置文件
- 给想要切换文字的Label对象下挂载Localize 关联Key
- 给用于切换语言的下拉列表下添加脚本LanguageSelection