---
title: "数据持久化-LitJson"
author: "mona"
date: 2024-07-11T14:48:00+08:00
categories: ["Unity"]
tags: ["数据持久化"]
---

## LitJson是什么

它是一个第三方库，用于处理Json的序列化和反序列化

/LitJson是C#编写的，体积小、速度快、易于使用

它可以很容易的嵌入到我们的代码中

只需要将LitJson代码拷贝到工程中即可

## 获取LitJson

前往LitJson官网

通过官网前往GitHub获取最新版本代码

将代码拷贝到Unity工程中 即可开始使用LitJson

## 使用LitJson进行序列化

```c#
//Litjson序列化方法
string jsonStr = JsonMapper.ToJson(t);
//将字符串保存到文件中
File.WriteAllText(Application.persistentDataPath + "/test.json", jsonStr);
```

**注意：**

1. 相对JsonUtlity不需要在自定义类中加特性
2. 不能序列化私有变量
3. 支持字典类型,字典的键 建议都是字符串
4. 需要引用LitJson命名空间
5. LitJson可以准确的保存null类型

## 使用LitJson反序列化

```c#
//读取json文件
string jsonStr = File.ReadAllText(Application.persistentDataPath + "/test.json");
//使用泛型 反序列化
Test test = JsonMapper.ToObject<Test>(jsonStr);
```

注意：

1. 类结构需要无参构造函数，否则反序列化时报错
2. 字典虽然支持 但是键在使用为数值时会有问题 需要使用字符串类型
3. Json文档编码格式必须是UTF-8
4. LitJson可以直接读取数据集合

