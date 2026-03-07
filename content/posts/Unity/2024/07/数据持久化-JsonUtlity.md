---
title: "数据持久化-JsonUtlity"
author: "mona"
date: 2024-07-11T14:48:00+08:00
categories: ["Unity"]
tags: ["数据持久化"]
---

## JsonUtlity是什么？

- JsonUtlity 是Unity自带的用于解析Json的公共类
- 它可以将内存中对象序列化为Json格式的字符串
- 将Json字符串反序列化为类对象

## 在文件中读取字符串

**把字符串存入文件中**

```c#
File.WriteAllText(Application.persistentDataPath + "/Test.json", "阿哈哈哈哈");
```

**在指定路径的文件中读取字符串**

```c#
string str = File.ReadAllText(Application.persistentDataPath + "/Test.json");//阿哈哈哈哈
```

## 使用JsonUtlity进行序列化

```c#
//JsonUtlity序列化方法 可以把对象 转为json字符串
string jsonStr = JsonUtility.ToJson(t);
//将字符串 保存到文件中
File.WriteAllText(Application.persistentDataPath + "/test.json", jsonStr);
```

**注意：**

1. float序列化时看起来会有一些误差
2. 成员中的自定义类需要加上序列化特性[System.Serializable]
3. 想要序列化私有变量 需要加上特性[SerializeField]
4. JsonUtility不支持字典
5. JsonUtlity存储null对象不会是null 而是默认值的数据

## 使用JsonUtlity进行反序列化

```c#
//读取路径中的json
jsonStr = File.ReadAllText(Application.persistentDataPath + "/test.json");
//使用Json字符串内容 转换成类对象
Test t2 = JsonUtility.FromJson(jsonStr, typeof(Test)) as Test;
//使用泛型的方式反序列化
Test t3 = JsonUtility.FromJson<Test>(jsonStr);
```

**注意：**

1. 如果Json中数据少了，读取到内存中类对象中时不会报错
2. 文本编码格式需要是UTF-8 不然无法加载
3. JsonUtlity不能直接将数据反序列化为数据集合

