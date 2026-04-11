---
title: "linux修改默认网卡名为eth0"
author: "mona"
date: 2026-04-11T18:35:48+08:00
categories: ["Linux"]
tags: []
---

### 第一步：修改内核引导参数

我们需要告诉内核禁用“一致性网络设备命名”规则。

```bash
# 1. 备份并修改 GRUB 配置文件
sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"/' /etc/default/grub
# 如果原本这一行就有内容，请手动编辑 /etc/default/grub 
# 将 net.ifnames=0 biosdevname=0 添加到引号内
```

### 第二步：更新 GRUB 并使配置生效

```bash
update-grub
```

### 第三步：修改网络接口配置文件

这一步至关重要，必须将配置文件里的 `ens17` 全部替换为 `eth0`，否则重启后系统找不到网卡。

```bash
# 3. 备份原始配置文件（万一出故障可以还原）
cp /etc/network/interfaces /etc/network/interfaces.bak

# 4. 使用 sed 批量替换 ens17 为 eth0
sed -i 's/ens17/eth0/g' /etc/network/interfaces
```

------

### 第四步：检查并重启

在重启之前，请务必确认文件修改正确：

```bash
# 5. 查看修改后的文件内容
cat /etc/network/interfaces
```

确认内容显示为： `auto eth0` `iface eth0 inet static` ... (其余信息不变)

```bash
# 6. 重启系统
reboot
```

------

### 验证结果

重启完成后，重新连接 SSH，输入：

```bash
ip addr
```

------

## 撤销安全补丁

```bash
vi /etc/default/grub
```

注意是修改 `GRUB_CMDLINE_LINUX_DEFAULT=` 

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash mitigations=off"
```

最后

```
update-grub
```

