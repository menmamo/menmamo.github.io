---
title: "Linux修改时区"
author: "mona"
date: 2020-12-20T19:41:00+08:00
categories: ["Linux"]
tags: []
---

```shell
rm -rf /etc/localtime 
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
date
```

