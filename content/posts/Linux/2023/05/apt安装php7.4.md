---
title: "apt安装php7.4"
author: "mona"
date: 2023-05-19T11:38:00+08:00
categories: ["Linux"]
tags: []
---

**安装php**

```shell
sudo apt install php7.4-fpm
sudo apt install php7.4-{sqlite3,curl,sockets,mbstring,iconv}
```

**调整php-fpm的进程数量**

```
pm = static
pm.max_children = 2
pm.max_requests = 500
```

**nginx开启php支持**

```nginx
location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
```

**开启伪链**

```nginx
location / {
    try_files $uri $uri/ /index.php?$args;
}
```