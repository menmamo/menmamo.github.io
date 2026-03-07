---
title: "Unity编辑器扩展-AssetDatabase公共类"
author: "mona"
date: 2024-08-08T21:49:00+08:00
categories: ["Unity"]
tags: ["编辑器扩展"]
---

## AssetDatabase公共类是用来做什么的？

它是 Unity 引擎中的一个编辑器类

用于在编辑器环境中管理和操作项目中的资源（Assets）

它提供了一系列静态方法

使得开发者能够在编辑器脚本中进行资源的创建、拷贝、移动、删除等操作

## 在哪里使用AssetDatabase公共类中的相关内容

在编辑器相关处都可以使用AssetDatabase公共类中的相关内容

它主要提供的是一些资源相关的辅助功能，可以在编辑器拓展开发的任意地方使用

但一定注意，它属于编辑器功能，无法被打包出去，只能在Unity编辑器中使用

## AssetDatabase API

### 创建资源

我们可以通过代码动态创建一些资源

路径从 Assets/...开始

AssetDatabase.CreateAsset(资源,路径);

**注意：**

不能在StreamingAssets中创建资源，

不能使用此方法创建预设体

只能创建资源相关，例如材质球等

路径需要写后缀

```c#
if(GUILayout.Button("创建资源"))
{
    Material mat = new Material(Shader.Find("Specular"));
    AssetDatabase.CreateAsset(mat, "Assets/Resources/MyMaterial.mat");
}
```

### 创建文件夹

路径从 Assets/...开始

AssetDatabase.CreateFolder(父文件夹，新文件夹名)

```c#
if (GUILayout.Button("创建文件夹"))
{
    AssetDatabase.CreateFolder("Assets/Resources", "MyTestFolder");
}
```

### 拷贝资源

路径从 Assets/...开始

AssetDatabase.CopyAsset(源资源，目标路径)

**注意：**

需要写后缀名

```c#
if (GUILayout.Button("拷贝资源"))
{
    AssetDatabase.CopyAsset("Assets/Editor Default Resources/EditorTeach.png", "Assets/Resources/MyTestFolder/EditorTeach.png");
}
```

### 移动资源

AssetDatabase.MoveAsset(老路径, 新路径);

```c#
if (GUILayout.Button("移动资源"))
{
    AssetDatabase.MoveAsset("Assets/Resources/MyTestFolder/EditorTeach.png", "Assets/Resources/EditorTeach.png");
}
```

**删除资源**

路径从 Assets/...开始

 AssetDatabase.DeleteAsset(资源路径)

```c#
if (GUILayout.Button("删除资源"))
{
    AssetDatabase.DeleteAsset("Assets/Resources/EditorTeach.png");
}
```

### 批量删除资源

路径从 Assets/...开始

AssetDatabase.DeleteAssets(string[] 路径们, List\<string\> 用于存储删除失败的路径)

```c#
if (GUILayout.Button("批量删除资源"))
{
    List<string> failList = new List<string>();
    AssetDatabase.DeleteAssets(new string[] { "Assets/Resources/EditorTeach.png", "Assets/Resources/EditorTeach2.png" }, failList);
    for (int i = 0; i < failList.Count; i++)
    {
        Debug.Log(failList[i]);
    }
}
```

### 获取资源路径

可以配合Selection选中资源一起使用

AssetDatabase.GetAssetPath(资源)

```c#
if (GUILayout.Button("获取资源路径"))
{
    Debug.Log(AssetDatabase.GetAssetPath(Selection.activeObject));
}
```

### 根据路径加载资源

 AssetDatabase.LoadAssetAtPath(资源路径)

```c#
if (GUILayout.Button("加载资源"))
{
    Texture txt = AssetDatabase.LoadAssetAtPath<Texture>("Assets/Resources/EditorTeach.png");
    Debug.Log(txt.name);
}
```

### 根据路径加载所有资源

AssetDatabase.LoadAllAssetsAtPath(资源路径);

一般可以用来加载图集资源，返回值为Object数据

 如果是图集，第一个为图集本身，之后的便是图集中的所有Sprite

```c#
if (GUILayout.Button(""))
{
    AssetDatabase.LoadAllAssetsAtPath("资源路径");
}
```

### 刷新

```c#
AssetDatabase.Refresh();
```

### 返回资源所属的AB包名

路径从Assets/开始

```c#
GetImplicitAssetBundleName（资源路径);
```

