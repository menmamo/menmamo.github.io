---
title: "优化VPS网络"
author: "mona"
date: 2026-03-25T22:59:00+08:00
categories: ["Linux"]
tags: []
---

## 计算TCP最大窗口大小

依据[cloudflare博客](https://blog.cloudflare.com/optimizing-tcp-for-high-throughput-and-low-latency/)计算BDP，以美西为例：带宽取1000Mbps延迟取200ms

```
BDP = 125MB/s × 0.2s = 25MB
```

得到25MB四舍五入为32MB。

由于 Linux 自动调优能够正确调整 RTT 较低的会话和吞吐量较低的瓶颈链路，我们只需要调整最大值即可（其他值为默认）。

`tcp_adv_win_scale` 使用默认的1。最大窗口大小为最大缓冲区空间的二分之一，所以需要将最大窗口大小设置为64M。

```
net.ipv4.tcp_rmem = 4096 131072 67108864
net.ipv4.tcp_wmem = 4096 16384 67108864
```

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

```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
net.ipv4.tcp_rmem = 4096 131072 67108864
net.ipv4.tcp_wmem = 4096 16384 67108864
net.ipv4.tcp_notsent_lowat = 131072
net.ipv4.tcp_slow_start_after_idle = 0
```
