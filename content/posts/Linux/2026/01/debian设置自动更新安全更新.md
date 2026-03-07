---
title: "debian设置自动更新安全更新"
author: "mona"
date: 2026-01-19T12:03:45+08:00
categories: ["Linux"]
tags: []
---

**安装必要软件包**

```shell
apt install unattended-upgrades
```

**启用自动更新功能**

```
dpkg-reconfigure -plow unattended-upgrades
```

这步操作会在 `/etc/apt/apt.conf.d/20auto-upgrades` 自动生成配置，确保更新服务会在后台运行。

**配置更新策略**

```
vi /etc/apt/apt.conf.d/50unattended-upgrades
```

```
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "04:00";
```

