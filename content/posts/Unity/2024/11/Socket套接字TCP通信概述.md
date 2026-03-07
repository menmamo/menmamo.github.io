---
title: "Socket套接字TCP通信概述"
author: "mona"
date: 2024-11-21T13:57:19+08:00
categories: ["Unity"]
tags: ["Unity网络开发"]
---

## 服务端和客户端需要做什么

### 客户端

1. 创建套接字Socket
2. 用Connect方法与服务端相连
3. 用Send和Receive相关方法收发数据
4. 用Shutdown方法释放连接
5. 关闭套接字

### 服务端

1. 创建套接字Socket
2. 用Bind方法将套接字与本地地址绑定
3. 用Listen方法监听
4. 用Accept方法等待客户端连接
5. 建立连接，Accept返回新套接字
6. 用Send和Receive相关方法收发数据
7. 用Shutdown方法释放连接
8. 关闭套接字

## TCP协议三次握手的体现

![](/usr/uploads/2024/11/4230308893.png)

![](/usr/uploads/2024/11/3507366322.png)

![](/usr/uploads/2024/11/2552593966.png)

## TCP协议四次挥手的体现

![](/usr/uploads/2024/11/2264902490.png)

![](/usr/uploads/2024/11/3544974811.png)

![](/usr/uploads/2024/11/1814195855.png)