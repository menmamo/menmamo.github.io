---
title: "Unity视频播放"
author: "mona"
date: 2024-09-05T11:09:00+08:00
categories: ["Unity"]
tags: ["Unity基础组件"]
---

## 视频剪辑导入相关设置

**sRGB**
是否开启sRGB
sRGB是微软联合惠普、三菱、爱普生等厂商联合开发的通用色彩标准
它的主要作用就是避免在不同设备出现色差
一般默认让它勾选即可

**Transcode**
是否开启转码

**Dimensions**
控制源文件内容的大小调整方式

- Original
  保持原始大小
- Three Quarter Res
  将源大小调整为其原始宽度和高度的四分之三
- Half Res
  将源大小调整为其原始宽度和高度的一半
- Quarter Res
  将源大小调整为其原始宽度和高度的四分之一
- Square(1024x1024)
  将源大小调整为1024x1024正方形图像。宽高比可控
- Square(512x512)
  将源大小调整为512x512正方形图像。宽高比可控
- Square(256x256)
  将源大小调整为256x256正方形图像。宽高比可控
- Custom
  将源大小调整为自定义分辨率。宽高比可控

**Codec**
选择用于转码的解编码器

- Auto
  为目标平台选择最合适的视频编解码器
- H264
  MPEG–4 高级视频编码 (AVC) 视频编解码器，受大多数平台上的硬件支持
- H265
  MPEG-H Part 2 或高效视频编码 (HEVC) 视频编解码器，受某些平台上的硬件支持
- VP8
  VP8视频编解码器，受到大多数平台上的软件支持，并受到 Android 和 WebGL 等几个平台上的硬件支持。

**Bitrate Mode**
相对于所选编解码器的基线配置文件的低、中或高比特率

**Spatial Quality**
此设置决定视频图像在转码过程中是否压缩大小，缩小意味着它们占用的存储空间更少。但是，调整图像大小也会导致在播放期间出现模糊。

**Keep Alpha**
保留Alpha透明通道并在转码期间将其编码
仅对具有Alpha通道的源显示此属性

注意：Android平台的原生 VP8 支持中不包括透明度支持，这意味着必须启用转码，以便 Unity 使用其内部 Alpha 表示方法。

**Deinterlace（去交错设置）**
控制隔行扫描源在转码期间如何解除隔行扫描

去交错设置，主要是为了优化动态画面的清晰度

- Off
  源文件没有隔行扫描，不进行隔行扫描
- Even
  采用每个帧的偶数行，将其插入以创建缺失内容，丢弃奇数行
- Odd
  采用每个帧的奇数行，将其插入以创建缺失内容，丢弃偶数行

**Filp Horizontally**
如果选中它，当转码时会将视频源文件内容沿水平轴翻转实现上下翻转

**Flip Vertically**
如果选中它，当转码时会将视频源文件内容沿垂直轴翻转实现左右翻转

**Import Audio**
只有存在音频轨道的视频才会出现该选项
如果勾选它，转码期间会导入音频轨道

------

## Video Player视频播放器组件参数

**Source视频源**

- Video Clip视频剪辑
- URL视频路径

**Play On Awake场景启动时就播放视频**
如果希望自己控制播放时机，请取消该选项

**Wait For First Frame**
如果勾选该选项，Unity将等待视频第一帧准备好显示，如果取消勾选，可能会丢弃前几帧使视频时间与游戏保持同步

**Loop**
是否循环播放

**Skip On Drop**
是否允许视频播放器跳过帧以赶上当前时间

**Playback Speed**
表示播放速度的乘数，0~10之间的值
1表示正常速度，如果数值为2，则视频速度*2，表示两倍速播放

**Render Mode渲染方式**

- Camera Far Plane
  在摄像机的远平面上渲染
- Camera Near Plane
  在摄像机的近平面上渲染
- Render Texture
  将视频渲染到渲染纹理中
- Material Override
  通过游戏对象渲染器的材质将视频渲染到游戏对象的选定纹理属性中
- API Only
  不预先设置渲染到哪里，通过代码来设置视频渲染到哪里
  通过Video Player中的 texture属性进行设置

**Aspect Ratio宽高比设置**

- No Scaling
  不使用缩放
- Fit Vertically
  垂直适应目标矩形
  必要时裁剪左侧和右侧或在每侧留下黑色区域
  以保留原视频的宽高比
- Fit Horizontally
  水平适应目标矩形
  必要时裁剪顶部和底部或在顶或底留下黑色区域
  以保留原视频的宽高比
- Fit Inside
  对原视频进行缩放而不必裁剪，但是可能会留黑边
- Fit Outside
  对原视频进行缩放，可能需要进行裁剪，但是不会留黑边
- Stretch
  在水平和垂直方向均进行缩放以适应目标矩形。不会保留源宽高比

**Audio Output Mode如何输出视频源的音频**

- None
  不播放音频
- Audio Source
  发送给指定的音频源对象，允许Unity的音频处理
- Direct
  直接绕过Unity音频处理，发送给音频输出硬件输出
- API Only
  通过代码将音频样本发送到关联的 AudioSampleProvider听诊器

**Track Enabled**
启用关联的音频轨道用于播放，必须在播放前设置
比如：
Track 0[en, 2 ch]表示有一个音频轨道，语言是 en， 并且音频轨道有2个声道（2 ch），表示它是双声道音频轨道

------

## Video Player代码相关

将 VideoPlayer 添加到摄像机对象上时， 它会自动瞄准摄像机背板，无需更改 videoPlayer.targetCamera。

```c#
GameObject camera = GameObject.Find("Main Camera");
videoPlayer = camera.AddComponent<VideoPlayer>();
```

**是否自动播放**

```c#
videoPlayer.playOnAwake = false;
```

**渲染模式**

```c#
videoPlayer.renderMode = VideoRenderMode.CameraFarPlane;
```

**设置目标 渲染贴图**

```c#
public RenderTexture texture;
videoPlayer.targetTexture = texture;
```

**设置目标摄像机**

```c#
videoPlayer.targetCamera
```

**透明度**

```c#
videoPlayer.targetCameraAlpha = 0.5f;
//videoPlayer.targetCamera3DLayout = Video3DLayout.OverUnder3D;
```

**视频源**

```c#
//设置clip
public VideoClip clip;
videoPlayer.source = VideoSource.VideoClip;
videoPlayer.clip = clip;
//设置url
videoPlayer.source = VideoSource.Url;
videoPlayer.url = Application.streamingAssetsPath + "/Video.mp4";
```

**是否循环**

```c#
videoPlayer.isLooping = false;
```

 **视频时长**

```c#
//  视频总时长
print(videoPlayer.length);//单位为s
//  当前时长,播放了多久
print(videoPlayer.time);//单位为s
//  总帧数
print(videoPlayer.frameCount);
//  当前帧
print(videoPlayer.frame);
```

**事件相关**

```c#
//  准备函数
videoPlayer.Prepare();
//  准备完成事件
videoPlayer.prepareCompleted += (v) =>
{
    print("准备完成");
    isOver = true;
};
//  开始事件
videoPlayer.started += (v) =>
{
    print("当执行player播放方法后 会调用的事件");
};

//  结尾时调用事件
videoPlayer.loopPointReached += (v) =>
{
    print("视频播放到结尾处时会调用的事件");
};
```

**播放、停止、暂停**

```c#
videoPlayer.Play();//播放
videoPlayer.Stop();//停止
videoPlayer.Pause();//暂停
```

------

## 全景视频播放

### Unity支持的全景视频

1. 等距圆柱投影布局(也称为球面投影、简化圆柱投影、矩形投影或普通圆柱投影)

   的全景视频(视频宽高比为 2:1的360度内容 或 1:1的180度内容)

2. 立方体贴图布局(六个正方形纹理的集合)

   的的全景视频(视频宽高比为1:6、3:4、4:3、6:1)

我们可以通过视频分辨率的比值判断该全景视频 为哪一种

### 在Unity中使用全景视频

1. 导入等距圆柱投影布局的视频文件
2. 用Video Player以Render Texture渲染纹理播放视频
3. 设置接受渲染纹理的天空盒材质,天空盒材质的着色器使用 Skybox>Panoramic
4. 设置场景以使用天空盒材质

注意：尽量使用较高分辨率率的全景视频（4K或8K）,这样效果更好

但是对于一些老设备或者移动设备可能最多只能使用2K分辨率，具体根据实际情况而定

### 使用全景视频时的注意事项

1. Render Texture渲染纹理的Size和视频尺寸一样，可以在视频预览窗口选择Source Info查看分辨率

2. 将渲染纹理的Depth Buffer设置为No depth buffer

3. 天空盒材质中

   - 等距圆柱投影布局，将Mapping设置为Latitude Longitude Layout

     根据视频时180视图还是360视图选择 360 degree或者180 degree

   - 立方体贴图，将Mapping设置为6 Frames Layout

4. 如果视频是VR视频

   分为了左右双眼，我们需要将天空盒材质的3D Layout设置为Side by Side

   如果左右侧内容在视频中上下分部，3D Layout设置为Over Under