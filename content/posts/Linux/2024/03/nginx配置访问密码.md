---
title: "nginx配置访问密码"
author: "mona"
date: 2024-03-12T16:00:00+08:00
categories: ["Linux"]
tags: []
---

1. 用openssl生成密钥：

```shell
openssl passwd 12345
```

2. 把密钥写入 htpasswd 文件，其中admin为用户名：

```shell
echo "admin:fIHcRVEKijgoM" > htpasswd
```

3. 修改nginx配置

```nginx
auth_basic "Please input password"; 
auth_basic_user_file /usr/share/nginx/passwd/htpasswd;
```
