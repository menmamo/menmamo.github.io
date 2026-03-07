---
title: "为vps添加虚拟内存"
author: "mona"
date: 2024-11-30T00:21:00+08:00
categories: ["Linux"]
tags: []
---

在运行内存不足的情况下，适当配置虚拟内存（Swap）可以缓解内存压力，提高系统稳定性。本文介绍如何在 VPS 上创建并启用 512MB 的交换空间。

## 停用旧的swap文件

```bash
sudo swapoff /swapfile
sudo rm /swapfile
```
 
---

## 步骤 1: 创建交换文件

首先，我们需要创建一个大小为 512MB 的文件用作交换空间。可以使用以下两种方法之一：

### 方法 1: 使用 `fallocate`（推荐）

```bash
sudo fallocate -l 512M /swapfile
```

### 方法 2: 使用 `dd`

如果 `fallocate` 命令不可用，可以改用 `dd`：

```bash
sudo dd if=/dev/zero of=/swapfile bs=1M count=512
```

------

## 步骤 2: 设置文件权限

为了安全起见，确保只有 root 用户可以访问交换文件：

```bash
sudo chmod 600 /swapfile
```

------

## 步骤 3: 配置交换空间

将文件设置为交换空间：

```bash
sudo mkswap /swapfile
```

------

## 步骤 4: 启用交换空间

执行以下命令立即启用交换空间：

```bash
sudo swapon /swapfile
```

------

## 步骤 5: 验证交换空间

使用 `free -h` 命令查看交换空间是否已成功启用：

```bash
free -h
```

输出示例：

```bash
              total        used        free      shared  buff/cache   available
Mem:          1.9Gi       1.2Gi       512Mi       128Mi       256Mi       512Mi
Swap:         512Mi          0B       512Mi
```

------

## 步骤 6: 持久化设置

为了确保交换空间在系统重启后仍然有效，需要将其添加到 `/etc/fstab` 文件中：

编辑文件：

```bash
sudo nano /etc/fstab
```

在文件末尾添加以下内容：

```bash
/swapfile swap swap defaults 0 0
```

保存并退出编辑器。

------

## 总结

通过以上步骤，我们成功为 VPS 配置了 512MB 的虚拟内存，系统性能在内存不足时将得到一定程度的优化。以下是完整的命令流程供快速参考：

```bash
sudo fallocate -l 512M /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile swap swap defaults 0 0' | sudo tee -a /etc/fstab
```