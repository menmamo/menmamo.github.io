---
title: "debian安装cloud内核"
author: "mona"
date: 2020-12-04T01:57:00+08:00
categories: ["Linux"]
tags: []
---

搜索内核

```
apt search linux-image | grep cloud-amd64
```

apt安装cloud内核

```shell
apt update && apt upgrade
apt install linux-image-cloud-amd64
reboot
```

卸载旧内核

```shell
uname -sr
Linux 4.12.0-041200-generic
```

列出系统安装的内核

```shell
dpkg -l | grep linux-image | awk '{print$2}'
linux-image-4.12.0-041200-generic
linux-image-4.8.0-22-generic
linux-image-extra-4.8.0-22-generic
linux-image-generic
```

卸载旧内核

```shell
sudo apt remove --purge linux-image-4.4.0-21-generic
sudo update-grub2
sudo reboot
```