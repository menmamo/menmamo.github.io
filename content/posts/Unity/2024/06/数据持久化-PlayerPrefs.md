---
title: "数据持久化-PlayerPrefs"
author: "mona"
date: 2024-06-17T11:40:00+08:00
categories: ["Unity"]
tags: ["数据持久化"]
---

## PlayerPrefs数据持久化

### 什么是数据持久化

数据持久化就是将 就是将内存中的数据模型转换为存储模型，以及将存储模型转换为内存中的数据模型的统称 例如将游戏数据存到硬盘、硬盘中的数据再读取到游戏中

### PlayerPrefs是什么？

PlayerPrefs是Unity提供用于存储读取玩家数据的公共类

### PlayerPrefs 使用方法

- PlayerPrefs的数据存储 类似于键值对存储 一个键对应一个值
- 提供了存储三种数据的类型 int float string
- 键：string类型
- 值：int float string 对应3中api

#### 添加数据

```C#
//参数 string key,int 值
PlayerPrefs.SetInt("myAge",18);//存到了内存中
PlayerPrefs.SetFloat("myHeight",188.5f);
PlayerPrefs.SetString("myName","路飞");
```

#### 保存数据

- 当游戏结束时Unity会自动把数据存到硬盘中
- 如果游戏不是正常结束的 而是崩溃 则会丢失数据
- 可调用.Save()方法手动保存到硬盘中

```c#
PlayerPrefs.Save();
```

#### PlayerPrefs的局限性

- PlayerPrefs是由局限性的 它只可以保存3种类型的数据
- 如果想要保存其他类型的数据 智能降低精度或上升精度

```C#
bool sex = true;
PlayerPrefs.SetInt("mySex",sex?1:0);
//键名一样 会被覆盖
PlayerPrefs.SetFloat("mySex",2);//
```

#### PlayerPrefs读取

- 运行时只要Set了对应的键值对
- 即使没有Save到本地 也可以读取出数据 从内存中

```c#
//如果 没有这个值 返回默认值 当int被float覆盖键相同也找不到
int age = PlayerPrefs.GetInt("myAge");
//指定默认值
int age = PlayerPrefs.GetInt("myAge",100);
```

#### 判断数据是否存在

- 一般是用来判断这个键对应的键值对数据是不是存在
- 如果存在就不创建新的

```c#
PlayerPrefs.HasKey("key");
```

#### 删除数据

```c#
PlayerPrefs.DeleteKey("key");
//删除所有数据
PlayerPrefs.DeleteAll("key");
```

### PlayPrefs存储的数据在哪里？

PlayPrefs再不同的平台下 存储的路径是不同的

### PlayPrefs数据唯一性

- PlayPrefs中不同数据的唯一性是由key决定的，不同的key决定了不同的数据
- 同一项目中 如果不同数据key相同 会造成数据丢失
- 所以要保证key的唯一性