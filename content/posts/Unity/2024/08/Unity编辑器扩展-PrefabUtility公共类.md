---
title: "Unity编辑器扩展-PrefabUtility公共类"
author: "mona"
date: 2024-08-08T21:51:00+08:00
categories: ["Unity"]
tags: ["编辑器扩展"]
---

## PrefabUtility公共类是什么

它是 Unity 编辑器中的一个公共类

提供了一些用于处理 Prefab（预制体或称预设体）的方法

主要功能包括 实例化预制体、创建预制体、修改预制体 等等

只要有对预制体操作的相关需求

它可以在编辑器开发的任何地方对其进行使用

## 常用API 

### 动态创建预设体

路径从Assets/...开始

```c#
//  PrefabUtility.SaveAsPrefabAsset(GameObject对象, 路径);
if(GUILayout.Button("动态创建预设体"))
{
    GameObject obj = new GameObject();
    obj.AddComponent<Rigidbody>();
    obj.AddComponent<BoxCollider>();
    PrefabUtility.SaveAsPrefabAsset(obj, "Assets/Resources/TestObj.prefab");
    DestroyImmediate(obj);
}
```

### 加载预制体对象

不能用于创建，一般用于修改，会把预设体加载到内存中

路径从Assets/...开始

```c#
//这里的加载 本质上其实已经把预设体进行了实例化了 只不过该实例化对象并不是在传统的Scene窗口中（是在一个看不见的独立的场景中）
if (GUILayout.Button("加载预制体对象"))
{
    //加载 到内存中 不能用来实例化 一般加载出来是进行修改的
    GameObject testObj = PrefabUtility.LoadPrefabContents("Assets/Resources/TestObj.prefab");
    testObj.AddComponent<MeshRenderer>();
    //这种加载方式 是可以进行脚本移除 子对象创建的
    DestroyImmediate(testObj.GetComponent<MeshRenderer>());
    GameObject obj = new GameObject("新建子对象");
    obj.transform.parent = testObj.transform;
    PrefabUtility.SaveAsPrefabAsset(testObj, "Assets/Resources/TestObj.prefab");
    //释放 一定要配合使用
    PrefabUtility.UnloadPrefabContents(testObj);
}
```

### 修改已有预设体

配合AssetDatabase.LoadAssetAtPath使用

这个方法不能存储实例化后的内容 只能存储对应的预设体对象

```c#
if (GUILayout.Button("修改已有预设体 "))
{
    GameObject testObj = AssetDatabase.LoadAssetAtPath<GameObject>("Assets/Resources/TestObj.prefab");
    testObj.AddComponent<BoxCollider>();
    //这个方法不能存储实例化后的内容 只能存储对应的预设体对象
    PrefabUtility.SavePrefabAsset(testObj);
}
```

### 实例化预设体

```c#
if (GUILayout.Button("实例化预设体"))
{
    GameObject testObj = AssetDatabase.LoadAssetAtPath<GameObject>("Assets/Resources/TestObj.prefab");
    PrefabUtility.InstantiatePrefab(testObj);

}
```

