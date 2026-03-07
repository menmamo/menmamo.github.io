---
title: "linux清理并保留最近两天的日志"
author: "mona"
date: 2025-12-04T12:07:00+08:00
categories: ["Linux"]
tags: []
---

## 查询 systemd 日志占用空间

```bash
journalctl --disk-usage
```

示例输出:

```bash
Archived and active journals take up 180.0M in the file system.
```

------

## 设置日志最大空间和只保留最近两天日志（自动清理）

```bash
sudo nano /etc/systemd/journald.conf
```

添加：

```bash
SystemMaxUse=200M
MaxRetentionSec=2day
```

重启服务

```bash
sudo systemctl restart systemd-journald
```

