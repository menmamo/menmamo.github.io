---
title: "Typecho批量替换图片路径为相对路径"
author: "mona"
date: 2025-12-04T22:24:00+08:00
categories: ["杂"]
tags: []
---

**Sqlite执行**

```
UPDATE typecho_contents SET text = REPLACE(text, 'https://域名', '');
```