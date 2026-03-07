---
title: "Unity编辑器扩展-AssetImporter和AssetPostprocessor"
author: "mona"
date: 2024-08-08T21:50:00+08:00
categories: ["Unity"]
tags: ["编辑器扩展"]
---

## AssetPostprocessor是什么？

AssetPostprocessor(资源后处理器类)

它主要用于处理资源导入时的通用逻辑

我们可以通过继承该类实现其中的一些回调方法来自定义处理资源

我们一般会进行以下处理：

1. 进行某种类型资源的通用设置
2. 对某种类型资源进行统一批量的处理

继承它后的常用属性：

- AssetImporter assetImporter：对应类型的资源导入器对象
- string assetPath: 导入资源的路径

### 继承它后的常用回调方法有：

**纹理相关**

- void OnPreprocessTexture()

  导入纹理资源之前调用，允许修改纹理的导入设置

- void OnPostprocessTexture(Texture2D texture)

  导入纹理资源之后调用，允许你对导入后为其进行后处理，比如 修改纹理格式、尺寸、压缩等等

**模型相关**

- void OnPreprocessModel()

  导入模型资源之前调用，允许修改纹理的导入设置

- void OnPostprocessModel(GameObject obj)

  导入模型资源之后调用，允许你对导入后为其进行后处理，比如 修改网格、材质、动画等

**音频相关**

- void OnPreprocessAudio()

  导入音频资源之前调用，允许修改纹理的导入设置

- void OnPostprocessAudio(AudioClip clip)

  导入音频资源之后调用，允许你对导入后为其进行后处理，比如 修改音频格式、质量等

等等等等

更多内容：https://docs.unity3d.com/ScriptReference/AssetPostprocessor.html

注意：
如果只想对某种资源中的某些内容进行处理 可以自己加命名规则

```c#
public class Test : AssetPostprocessor
{
     //纹理相关
    // 导入纹理资源之前调用，允许修改纹理的导入设置
    void OnPreprocessTexture()
    {
        //1.资源导入批量设置（对导入的资源进行统一设置）
        Debug.Log("纹理设置回调" + assetPath);
        TextureImporter improter = assetImporter as TextureImporter;
        improter.textureType = TextureImporterType.Sprite;
        improter.mipmapEnabled = false;
    }
    //  导入纹理资源之后调用，允许你对导入后为其进行后处理，比如 修改纹理格式、尺寸、压缩等等
    void OnPostprocessTexture(Texture2D texture)
    {
        //2.资源导入后处理（对导入的资源进行统一的预处理）
        Debug.Log("纹理后处理回调" + texture.name);
        EditorUtility.CompressTexture(texture, TextureFormat.ETC_RGB4, TextureCompressionQuality.Fast);
    }

    //模型相关
    //  导入模型资源之前调用，允许修改纹理的导入设置
    void OnPreprocessModel()
    {
        ModelImporter improter = assetImporter as ModelImporter;
    }
    //  导入模型资源之后调用，允许你对导入后为其进行后处理，比如 修改网格、材质、动画等
    void OnPostprocessModel(GameObject obj)
    {

    }

    //音频相关
    //  导入音频资源之前调用，允许修改纹理的导入设置
    void OnPreprocessAudio()
    {
        AudioImporter improter = assetImporter as AudioImporter;

    }
    //  导入音频资源之后调用，允许你对导入后为其进行后处理，比如 修改音频格式、质量等
    void OnPostprocessAudio(AudioClip clip)
    {

    }
    
}
```

## AssetImporter公共类

特定资源类型的资源导入程序的基类

它提供了一些方法和属性，用于配置和管理资源的导入设置

一般我们不会直接使用该类，而是通过使用继承它的子类来设置导入资源的相关信息

当我们导入一个资源时，在Inspector窗口中进行的相关设置

都是通过继承该类的子类实现的

它的子类一般按照资源类型来划分：

- TextureImporter

  用于导入纹理资源，并配置纹理的压缩格式、尺寸、平铺方式等设置

- ModelImporter

  用于导入模型资源，并配置模型的导入设置，如网格、材质、动画等

- AudioImporter

  用于导入音频资源，并配置音频的导入设置，如压缩格式、音频质量等

- VideoClipImporter

  用于导入视频资源，并配置视频的导入设置，如视频质量、循环模式等

- ScriptedImporter

  用于创建自定义的资源导入器，可以通过编写脚本来实现对特定类型资源的导入设置和处理逻辑

  如果想要对某些特定格式的资源进行自定义配置处理，可以通过继承该类的方式去实现

API说明：

https://docs.unity3d.com/ScriptReference