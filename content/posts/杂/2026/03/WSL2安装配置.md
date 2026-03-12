---
title: "WSL2安装配置"
date: 2026-03-13T00:18:00+08:00
tags: []
categories: [杂"]
---

## 1. 基础安装与核心命令

在 Windows 中，WSL 的安装已经变得极其简单。只需在管理员权限的 PowerShell 中执行：

### 常用管理命令
| 功能               | 命令                             |
| :----------------- | :------------------------------- |
| **一键安装**       | `wsl --install`                  |
| **内核更新**       | `wsl --update`                   |
| **查看已安装系统** | `wsl --list -v`                  |
| **设置默认系统**   | `wsl --set-default <DistroName>` |
| **彻底卸载系统**   | `wsl --unregister <DistroName>`  |

---

## 2. 开启「镜像网络模式」 (Mirrored Mode)

这是 Windows 11 引入的重磅功能，解决了 WSL 无法直接访问宿主机 VPN、IP 变动等痛点。

### 配置步骤
1. 打开用户主目录：按 `Win + R` 输入 `%UserProfile%`。
2. 创建文件：`.wslconfig` (确保无 `.txt` 后缀)。
3. 填入以下高性能配置：

```ini
[wsl2]
# 开启镜像网络模式，WSL 将共享 Windows 的 IP
networkingMode=mirrored
# 开启 DNS 隧道，解决 VPN 导致的无法上网问题
dnsTunneling=true
# 开启防火墙，让 Windows 安全策略同步生效
firewall=true
# 自动同步 Windows 代理设置
autoProxy=true
```

> **注意**：配置保存后，必须执行 `wsl --shutdown` 重启才能生效。

------

## 3. 系统的备份与恢复

为了防止配置环境崩溃，定期备份是非常有必要的。

### 备份 (Export)

```PowerShell
wsl --export Debian D:\backup\debian_bak.tar
```

### 恢复 (Import)

```PowerShell
wsl --import Debian_New D:\WSL_Data D:\backup\debian_bak.tar
```

------

## 4. 跨平台互操作 (Interop)

WSL 的强大之处在于它能与 Windows 发挥“组合拳”优势。

### 在 Linux 中调用 Windows 工具

修改 `/etc/wsl.conf`：

```Ini
[interop]
enabled = true
appendWindowsPath = true
```

**实战演示**：

```Bash
# 在 Linux 终端里直接打开记事本编辑文件
notepad.exe test.txt
```

### 在 Windows 中直接使用 Linux 命令

```PowerShell
# 在 PowerShell 里通过 grep 过滤文件
Get-ChildItem | wsl grep "config"
```

------

## 5. 进阶特性：WSLG 与 GPU 直通

- **WSLG**: 无需额外安装 XServer，直接在 Linux 里安装 GUI 应用（如 `sudo apt install gimp`）即可在 Windows 窗口运行。
- **显卡直通**: 在 Linux 中直接运行 `nvidia-smi`。如果能看到显卡状态，说明你的深度学习环境已经就绪。

------

**结语**：WSL2 已经不再是一个简单的“虚拟机”，它是开发者在 Windows 上进行全栈开发的最优解。
