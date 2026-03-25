---
title: "优化VPS网络"
author: "mona"
date: 2026-03-25T22:59:00+08:00
categories: ["Linux"]
tags: []
---

## 调整TCP窗口大小

根据[cloudflare](https://blog.cloudflare.com/optimizing-tcp-for-high-throughput-and-low-latency/)的博客计算BDP

带宽:1000Mbps = 125MB/s   延迟：200ms = 0.2s

BDP = 125MB/s × 0.2s = 25MB 四舍五入为32MB

因 `tcp_adv_win_scale` 设置为 -2 最大窗口大小为最大缓冲区空间的四分之一。

所以设置最大读写缓冲区的大小为 32×4 = 128M 换算成字节134217728

日本选择100ms作为参考值 最终值为67108864

------

## 开启BBR

在bbr打开时，如果长连接比较多 设置`net.ipv4.tcp_slow_start_after_idle = 0`这样子空闲一段时间，之前bbr估算的拥塞窗口大小也不会从头探测

```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
net.ipv4.tcp_slow_start_after_idle = 0
```

------

## 最终参数

**美国**

```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
net.ipv4.tcp_slow_start_after_idle = 0
net.ipv4.tcp_rmem = 8192 262144 134217728
net.ipv4.tcp_wmem = 4096 16384 134217728
net.ipv4.tcp_adv_win_scale = -2
net.ipv4.tcp_notsent_lowat = 131072
```

**日本**

```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
net.ipv4.tcp_slow_start_after_idle = 0
net.ipv4.tcp_rmem = 8192 262144 67108864
net.ipv4.tcp_wmem = 4096 16384 67108864
net.ipv4.tcp_adv_win_scale = -2
net.ipv4.tcp_notsent_lowat = 131072
```
