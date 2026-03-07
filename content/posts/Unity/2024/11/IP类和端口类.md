---
title: "IP类和端口类"
author: "mona"
date: 2024-11-15T14:04:00+08:00
categories: ["Unity"]
tags: ["Unity网络开发"]
---

## IP类和端口类用来做什么？

如果要进行网络通信、网络连接首先需要找到对应的设备，IP和端口号是定位网络中设备必不可少的关键元素

C#中提供了对应的IP和端口相关的类

------

## IPAddress类

命名空间：`System.Net`

类名：`IPAddress`

**初始化**

**使用`byte`数组进行初始化**

```c#
byte[] ipAddress = new byte[] {192,168,1,1};
IPAddress ip = new IPAddress(ipAddress);
```

**使用`long`长整型进行初始化**

```c#
//4字节对应的长整型 一般不建议使用
IPAddress ip = new IPAddress(0xC0A80101);//192.168.1.1
```

**使用字符串转换（推荐）**

```c#
IPAddress ip = IPAddress.Parse("192.168.1.1");
```

**特殊的IP地址**

`127.0.0.1`代表本机地址

**获取可用的IPV6地址**

```c#
IPAddress.IPv6Any
```

------

## IPEndPoint类

命名空间：`System.Net`

类名：`IPEndPoint`

`IPEndPoint`类将网络端点表示为IP地址和端口号，表现为IP地址和端口号的组合

**初始化**

```c#
//方法1：
IPEndPoint ipPoint = new IPEndPoint(0xC0A80101,8080);
//192.168.1.1:8080 long长整型的ip地址
//方法2(推荐)：
IPEndPoint ipPoint = new IPEndPoint(IPAddress.Parse("192.168.1.1"),8080);
```

