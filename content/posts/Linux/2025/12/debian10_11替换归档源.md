---
title: "debian10/11替换归档源"
author: "mona"
date: 2025-12-02T23:32:59+08:00
categories: ["Linux"]
tags: []
---

## 允许过期仓库

```shell
echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99ignore-release-date
```

------

### 修改仓库源

**debian10**

```
deb http://archive.debian.org/debian/ buster main contrib non-free
deb-src http://archive.debian.org/debian/ buster main contrib non-free

deb http://archive.debian.org/debian-security/ buster/updates main contrib non-free
deb-src http://archive.debian.org/debian-security/ buster/updates main contrib non-free

deb http://archive.debian.org/debian/ buster-updates main contrib non-free
deb-src http://archive.debian.org/debian/ buster-updates main contrib non-free

deb http://archive.debian.org/debian/ buster-backports main contrib non-free
deb-src http://archive.debian.org/debian/ buster-backports main contrib non-free
```

**debian11**

LTS 阶段源

```
deb http://deb.debian.org/debian bullseye main contrib non-free
deb-src http://deb.debian.org/debian bullseye main contrib non-free

deb http://security.debian.org/debian-security bullseye-security main contrib non-free
deb-src http://security.debian.org/debian-security bullseye-security main contrib non-free

deb http://deb.debian.org/debian bullseye-updates main contrib non-free
deb-src http://deb.debian.org/debian bullseye-updates main contrib non-free

# backports 归档
deb http://archive.debian.org/debian bullseye-backports main contrib non-free
deb-src http://archive.debian.org/debian bullseye-backports main contrib non-free
```

lts后期

```
deb http://archive.debian.org/debian bullseye main contrib non-free
deb-src http://archive.debian.org/debian bullseye main contrib non-free

deb http://archive.debian.org/debian-security bullseye-security main contrib non-free
deb-src http://archive.debian.org/debian-security bullseye-security main contrib non-free

deb http://archive.debian.org/debian bullseye-updates main contrib non-free
deb-src http://archive.debian.org/debian bullseye-updates main contrib non-free

deb http://archive.debian.org/debian bullseye-backports main contrib non-free
deb-src http://archive.debian.org/debian bullseye-backports main contrib non-free
```

