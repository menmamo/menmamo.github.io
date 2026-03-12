---
title: "告别繁琐安装Winget教程"
author: "mona"
date: 2026-03-13T00:27:00+08:00
categories: ["杂"]
tags: []
---

### 一、 什么是 Winget？

**winget** 是微软推出的开源软件包管理器。它允许用户通过简单的命令行操作，实现软件的**搜索、安装、升级、卸载和配置**。

**为什么要用它？**

- **官方背书：** 安全、可靠，无需担心广告插件。
- **高效装机：** 一行代码安装几十款常用软件。
- **自动升级：** 告别手动检查更新的烦恼。
- **原生集成：** 现代 Windows 10/11 系统已内置。

------

### 二、 基础操作：从安装到卸载

打开你的 **PowerShell** 或 **命令提示符 (CMD)**，开启你的命令行装机之旅。

#### 1. 查找软件

不知道软件的确切名称？先搜一下：

```PowerShell
winget search <软件名>
```

> **提示：** 如果结果较多，可以使用 `winget search "Google Chrome"` 这样带引号的精确匹配。

#### 2. 安装软件

找到软件后，使用 `install` 命令：

```PowerShell
# 以安装 VS Code 为例
winget install "Visual Studio Code"
```

如果你想静默安装（不弹出安装界面），可以加上参数：

```PowerShell
winget install vscode --silent
```

#### 3. 升级软件

这是 winget 最强大的功能之一。

- **检查可更新项目：** `winget upgrade`

- **升级单个软件：** `winget upgrade <软件名>`

- **一键升级所有软件：**

  ```powershell
  winget upgrade --all
  ```

#### 4. 卸载软件

再也不用去控制面板翻找列表了：

```PowerShell
winget uninstall <软件名>
```

------

### 三、 进阶技巧：批量装机与导出

当你拿到一台新电脑，或者重装了系统，winget 能帮你节省数小时的时间。

#### 1. 导出当前软件列表

你可以将旧电脑上的软件清单导出为一个 JSON 文件：

```PowerShell
winget export -o D:\my_apps.json
```

#### 2. 导入并自动安装

在新电脑上，直接运行导入命令，winget 会自动下载并安装列表里的所有软件：

```PowerShell
winget import -i D:\my_apps.json
```

------

### 四、 常用命令速查表

| **功能**         | **命令**                  |
| ---------------- | ------------------------- |
| **搜索**         | `winget search <name>`    |
| **安装**         | `winget install <name>`   |
| **卸载**         | `winget uninstall <name>` |
| **列出已安装**   | `winget list`             |
| **检查更新**     | `winget upgrade`          |
| **全部更新**     | `winget upgrade --all`    |
| **查看软件信息** | `winget show <name>`      |

------

### 五、 常见问题 (FAQ)

**Q: 提示找不到 winget 命令怎么办？**

A: 请前往 [Microsoft Store](https://www.google.com/search?q=https://www.microsoft.com/store/productId/9NBLGGH4R32N) 搜索并更新 **“应用安装程序” (App Installer)**。

**Q: 安装路径可以修改吗？**

A: 目前 winget 主要遵循软件包原生的安装路径。不过，部分交互式安装包在安装时仍会弹出路径选择框。

**Q: 权限不足？**

A: 大部分安装操作需要管理员权限。建议**以管理员身份运行** PowerShell。

------

### 结语

Winget 正在变得越来越强大，它不仅简化了工作流，也让 Windows 的生态环境向开发者更加靠拢。如果你还没试过，现在就打开终端，输入 `winget upgrade --all` 感受一下自动化的魅力吧！