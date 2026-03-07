---
title: "Linux文件夹压缩与解压"
author: "mona"
date: 2024-11-12T14:37:00+08:00
categories: ["Linux"]
tags: []
---

在 Linux 系统中，压缩和解压文件夹是日常运维中常见的操作。本文将详细介绍如何使用 `zip` 和 `tar` 命令进行文件夹的压缩与解压，包括 `.zip`、`.tar.gz` 和 `.tar.bz2` 等格式。

## 压缩文件夹

### 使用 `zip` 压缩文件夹

```bash
zip -r 压缩文件名.zip 文件夹名
```

#### 压缩到指定目录

```bash
zip -r /path/to/destination/myfolder.zip 文件夹名
```

------

### 使用 `tar` 压缩文件夹

`tar` 命令可以将文件夹压缩为 `.tar.gz` 或 `.tar.bz2` 格式。

#### 压缩为 `.tar.gz`

```bash
tar -czvf 压缩文件名.tar.gz 文件夹名
```

#### 压缩为 `.tar.bz2`

```bash
tar -cjvf 压缩文件名.tar.bz2 文件夹名
```

------

## 解压文件夹

### 解压 `.zip` 文件

使用 `unzip` 命令解压 `.zip` 文件。

#### 解压到当前目录

```bash
unzip 压缩文件名.zip
```

#### 解压到指定目录

```bash
unzip 压缩文件名.zip -d 目标目录
```

------

### 解压 `.tar.gz` 文件

#### 解压到当前目录

```bash
tar -xzvf 压缩文件名.tar.gz
```

#### 解压到指定目录

```bash
tar -xzvf 压缩文件名.tar.gz -C 目标目录
```

------

### 解压 `.tar.bz2` 文件

#### 解压到当前目录

```bash
tar -xjvf 压缩文件名.tar.bz2
```

#### 解压到指定目录

```bash
tar -xjvf 压缩文件名.tar.bz2 -C 目标目录
```

------

## 总结

| 格式       | 压缩命令                                | 解压命令                       |
| ---------- | --------------------------------------- | ------------------------------ |
| `.zip`     | `zip -r 压缩文件名.zip 文件夹名`        | `unzip 压缩文件名.zip`         |
| `.tar.gz`  | `tar -czvf 压缩文件名.tar.gz 文件夹名`  | `tar -xzvf 压缩文件名.tar.gz`  |
| `.tar.bz2` | `tar -cjvf 压缩文件名.tar.bz2 文件夹名` | `tar -xjvf 压缩文件名.tar.bz2` |

通过本文的教程，你可以轻松地压缩或解压文件夹。根据需求选择合适的工具和格式，操作更加高效！