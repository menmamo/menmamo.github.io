---
title: "音效组件AudioSources"
author: "mona"
date: 2024-06-17T11:44:00+08:00
categories: ["Unity"]
tags: ["音频组件"]
---

## 音效系统

### Unity支持的音频格式

- wav
- mp3
- ogg
- aiff

### 音频文件属性设置

- Force To Mono 多声道转单声道
  - Normalize 转换为单声道时 混合过程中被标准化
- Load In Background在后台加载 不阻塞主线程
- Ambisonic 立体混响声 适合360度视频和xr应用程序 如果音频文件包含立体混响声编码 请启用此选项
- LoadType加载类型 可根据不同的平台单独设置
  - Decompress On Load 不压缩的形式存在内存 加载快 但是占用内存较高 适用小音效
  - Compress in memory 压缩形式存在内存 加载慢 内存小 适用于较大的音效文件
  - Streaming 以流形式存在 使用时解码 占用内存小 cpu消耗高 性能换内存
- Preload Audio Data 预加载音频 勾选后进入场景就加载 不勾选 第一次使用才加载
- Compression Format 压缩方式
  - PCM音频以最高质量存储
  - Vorbis相对PCM压缩的更小 根据质量决定
  - ADPCM 包含噪音，会被多次播放的声音，如碰撞声
- Quality音频质量 确定要应用于压缩剪辑的压缩量 不适用于PCM/ADPCM/HEVAG格式
- Sample Rate Setting 压缩个是允许自动优化或说动降低采样率
  - Preserve Sample Rate 此设置可保持采样率不变（默认值）
  - Optimize Sample Rate 此设置根据分析的最高频率内容自动优化采样率
  - Override Sample Rate 手动设置采样率

### AudioSource

- 需要挂载AudioListener才能听到声音
- 一个对象可以挂载多个AudioSource
  - 使用时需要注意 如果挂载多个AudioSource 那一定要自己管理他们 控制他们的播放停止 不然我们没有办法准确的获取谁是谁

### AudioSource组件面板属性

- AudioClip 声音剪辑文件（音频文件）
- Putput 默认将直接输出到场景中的音频监听器 可以更改为输出到混音器
- Mute 静音开关
- Bypass Effect开关滤波器效果
- Bypass Listener Effects 快速开关所有监听器
- Bypass Reverb Zones 快速开关所有混响区
- Play On Awake 对象创建时就播放音乐
- Loop循环
- Priority优先级
- Volume音量大小
- Pitch音高
- Stereo Pan  2D声音立体声位置 相当于左右声道
- Spatial Blend 音效受3D空间的影响程度
- Reverb Zone Mix 到混响区的信号输出量
- 3D Sound Setttings 和Spatial Blend参数成正比应用
  - Doppler Level 多普勒效果等级
  - Spread 扩散角度设置为3D立体声还是多声道
  - Volume Rolloff 声音衰减速度
    - Logarithmic Rolloff 靠近音源时声音很大 离开时 降低的很快
    - Linear Rolloff 与音频源距离越远 听到的声音越小
    - Custom Rolloff 音频源的音频效果是根据曲线图的设置变化的
  - Min/Max Distance 
    - 最小距离内 声音保持最大的响度
    - 最大距离外 声音开始减弱

### 代码控制音频源

#### 获取AudioSource

```c#
//获取AudioSource
audioSource = GetComponent<AudioSource>();
```

#### 控制播放暂停

```c#
audioSource.Play();//播放
audioSource.PlayDelayed(5);//5秒后播放 单位秒
audioSource.Stop();//停止
audioSource.Pause();//暂停
//停止暂停 和暂停后play是一个效果
audioSource.UnPause();
```

#### 如何检测音效播放完毕

```c#
if(audioSource.isPlaying){
    Print("播放中");
}
else{
     Print("播放结束");
}
```

#### 使用一个AudioSource控制播放不同的音效

```c#
public AudioClip clip;
audioSource.clip = clip;
audioSource.Play();
```

### 麦克风输入相关

#### 获取设备麦克风信息

```c#
String[] strs = Microphone.devices;
```

#### 开始录制

```c#
//参数1：设备名 如果使用默认设备传null
//参数2：超过录制长度后是否重新入职 一般faslse
//参数3：录制长度 单位秒
//参数4：采样率
//返回一个AudioClip
AudioClip clip = Microphone.Start(null,false,10,44100);
```

#### 结束录制

```c#
//参数：设备名
Microphone.End(null);
```

### AudioClip

#### 获取音频数据

```c#
//规则 用于存储数组数据的长度 是用 声道数*剪辑长度
float[] f = new float[clip.channels * clip.samples];
clip.GetData(f,0);
```