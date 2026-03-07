---
title: "Unity编辑器扩展-EditorUtility公共类(提示窗文件选择框等)"
author: "mona"
date: 2024-08-08T15:08:00+08:00
categories: ["Unity"]
tags: ["编辑器扩展"]
---

## EditorUtility公共类

它是 Unity 编辑器中的一个实用工具类

提供了一系列用于编辑器脚本和自定义编辑器的实用功能

在编辑器相关处都可以使用EditorUtility公共类中的相关内容

它主要提供的是一些辅助功能，可以在编辑器拓展开发的任意地方使用

但一定注意，它属于编辑器功能，无法被打包出去，只能在Unity编辑器中使用

### EditorUtility API

**显示提示窗口**

```c#
//EditorUtility.DisplayDialog("标题", "显示信息", "确定键名");
//注意：窗口显示会阻塞逻辑
private void OnGUI()
{
    if(GUILayout.Button("显示提示窗口"))
    {
        if(EditorUtility.DisplayDialog("测试窗口", "确定一定要做这件事情吗", "一定要做"))
        {
            Debug.Log("确定要做，在这里去处理逻辑");
        }
        else
        {
            Debug.Log("点击了叉叉，不去做");
        }
        Debug.Log("窗口显示完毕");
    }
}
```

 **显示三键提示面板**

```c#
//  int EditorUtility.DisplayDialogComplex("标题", "显示信息", "按钮1名字", "按钮3名字", "按钮2名字");
//  返回值 0-按钮1按下 1-按钮3按下 2-按钮2按下
//注意：窗口显示会阻塞逻辑
private void OnGUI()
{
    if(GUILayout.Button("显示三键提示窗口"))
    {
        int result = EditorUtility.DisplayDialogComplex("三键提示", "显示信息", "选项1", "关闭", "选项2");
        switch (result)
        {
            case 0:
                Debug.Log("选项1按下了");
                break;
            case 1:
                Debug.Log("关闭键按下了");
                break;
            case 2:
                Debug.Log("选项2按下了");
                break;
            default:
                break;
        }
        Debug.Log("三键窗口显示完毕");
    }
}
```

 **进度条相关**

```c#
//显示进度条
//EditorUtility.DisplayProgressBar("进度条", "显示信息", 进制值0~1);
//关闭进度条
//EditorUtility.ClearProgressBar();
//注意：进度条窗口不会卡逻辑，但是需要配合关闭进度条使用
private void OnGUI()
{
    if(GUILayout.Button("显示更新进度条"))
    {
        value += 0.1f;
        EditorUtility.DisplayProgressBar("进度条标题", "进度条窗口显示内容", value);
        Debug.Log("进度条窗口显示完毕");
    }

    if(GUILayout.Button("关闭进度条"))
    {
        value = 0;
        EditorUtility.ClearProgressBar();
    }
}
```

**显示 文件 存储面板**

```c#
//通常用于在编辑器中保存新创建的文件或选择文件的保存路径
//string path = EditorUtility.SaveFilePanel("窗口标题", "默认打开的目录", "保存的文件的名称", "文后缀格式")
if(GUILayout.Button("打开文件存储面板"))
{
    string str = EditorUtility.SaveFilePanel("保存我的文件", Application.dataPath, "Test", "txt");
    Debug.Log(str);
    if(str != "")
        File.WriteAllText(str, "123123123123123");
}
```

**显示 文件 存储面板(指定在Asset文件夹中)**

```c#
//与 SaveFilePanel 类似，但是它将保存路径限制在项目目录中，只允许用户选择项目内的文件夹作为保存路径
//string path = EditorUtility.SaveFilePanelInProject("窗口标题", "保存的文件的名称", "后缀格式", "在对话框窗口中显示的文本摘要");
if(GUILayout.Button("打开文件存储面板（仅限工程文件夹下）"))
{
    string str2 = EditorUtility.SaveFilePanelInProject("保存项目内的文件", "Test2", "png", "自定义文件");
    Debug.Log(str2);
}
```

**显示 文件夹 存储面板**

```c#
//通常用于在编辑器中选择文件夹作为保存路径，用于保存文件或执行其他与文件夹相关的操作
//string path = EditorUtility.SaveFolderPanel("窗口标题", "文件夹", "默认名称");
if (GUILayout.Button("显示文件夹存储面板"))
{
    string str3 = EditorUtility.SaveFolderPanel("得到一个存储路径（文件夹）", Application.dataPath, "");
    Debug.Log(str3);
}
```

**显示打开 文件 面板**

```c#
//通常用于在编辑器中选择文件进行打开或执行其他与文件相关的操作
//string path = EditorUtility.OpenFilePanel("窗口标题", "文件路径", "后缀格式");
if (GUILayout.Button("显示打开文件面板"))
{
    string str4 = EditorUtility.OpenFilePanel("得到一个文件路径", Application.dataPath, "txt");
    if(str4 != "")
    {
        string txt = File.ReadAllText(str4);
        Debug.Log(txt);
    }
}
```

**显示打开 文件夹 面板**

```c#
//通常用于在编辑器中选择文件夹进行打开或执行其他与文件夹相关的操作
//string path = EditorUtility.OpenFolderPanel("窗口标题", "文件夹", "默认名称");
if (GUILayout.Button("显示打开文件夹面板"))
{
    string str4 = EditorUtility.OpenFolderPanel("得到一个文件路径", Application.dataPath, "");
    if (str4 != "")
    {
        Debug.Log(str4);
    }
}
```

**压缩纹理**

```c#
EditorUtility.CompressTexture(Texture2D texture, TextureFormat format, TextureCompressionQuality quality);
//可以将纹理显式压缩为指定的格式
```

**查找对象依赖项**

```c#
 EditorUtility.CollectDependencies(Object[] roots);//返回对象所依赖的所有资源列表
```

