---
title: "ScriptableObject"
author: "mona"
date: 2024-07-23T16:11:00+08:00
categories: ["Unity"]
tags: ["Unity基础组件"]
---

## ScriptableObject是什么

ScriptableObject是Unity提供的一个数据配置存储基类

他是一个可以用来保存大量数据的数据容器

就像是可以自定义的数据资源文件

他是一个类似MonoBehvior的基类

需要我们继承他来使用

## ScriptableObject的作用

1. 数据复用（多个对象可以使用一个配置）
2. 配置文件（配置数据中的数据）
3. 编辑模式下的数据持久化

## ScriptableObject的好处

1. 我们可以直接在Inspector窗口编辑配置数据，可以利用它来做配置文件
2. 处理重复数据，减少数据拷贝时造成的内存占用，可以利用它来做公共数据
3. 可以方便的处理数据带来的多态行为

## 创建ScriptableObject数据容器

1. 继承ScriptableObject类
2. 在该类中声明成员（变量，方法等）

```c#
public clss Test : ScriptableObject
{
    public int a;
}
```

## 根据自定义的ScriptableObject数据容器创建数据文件

**方法1：为类添加CreateAssetMenu通过菜单创建资源特性**

```c#
[CreateAssetMenu(fileName = "默认文件名", menuName = "在Asset/Create菜单中显示的名字", order = 再Asset/Create菜单中的位置(多个时可以通过它来调整顺序))]
public clss Test : ScriptableObject
{
    public int a;
}
```

**方法2：利用ScriptableObject的静态方法创建数据对象**

```c#
public class ScriptableObjectTool
{
    [MenuItem("ScriptableObject/CreateMyData")]
    public static void CreateMyData()
    {
        //书写创建数据资源文件的代码
        MyData asset = ScriptableObject.CreateInstance<MyData>();
        //通过编辑器API 根据数据创建一个数据资源文件
        AssetDatabase.CreateAsset(asset, "Assets/Resources/MyDataTest.asset");
        //保存创建的资源
        AssetDatabase.SaveAssets();
        //刷新界面
        AssetDatabase.Refresh();
    }
}
```

## ScriptableObject数据文件的使用

1. Inspector面板中拖拽
2. 通过资源加载的方式获取

```c#
 MyData data = Resources.Load<MyData>("MyDataTest");
```

## ScriptableObject的生命周期函数

- Awake 数据文件创建时调用
- OnDestroy ScriptableObject 对象将被销毁时调用
- OnDisable ScriptableObject 对象销毁时、即将重新加载脚本程序集时 调用
- OnEnable ScriptableObject 创建或者加载对象时调用
- OnValidate 编辑器才会调用的函数，Unity在加载脚本或者Inspector窗口中更改值时调用

## 在游戏运行中创建ScriptableObject对象（非持久化）

```c#
MyData data;
data = ScriptableObject.CreateInstance("MyData") as MyData;
//泛型
data = ScriptableObject.CreateInstance<MyData>();
```

