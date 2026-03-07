---
title: "acme申请证书"
author: "mona"
date: 2026-01-24T23:04:00+08:00
categories: ["Linux"]
tags: []
---

**安装**

```
curl https://get.acme.sh | sh -s email=my@example.com
source ~/.bashrc
```

**申请证书**

```
acme.sh --issue -d my.domain.com --nginx
```

**自动重载nginx**

```
acme.sh --install-cert -d my.domain.com \
--key-file       /usr/share/nginx/ssl/my.domain.com.key \
--fullchain-file /usr/share/nginx/ssl/fullchain.cer \
--reloadcmd     "nginx -t && systemctl reload nginx"
```

------

**CF的方式申请泛域名证书**

导入环境变量

```
export CF_Token=""
export CF_Account_ID=""
export CF_Zone_ID=""
```

```
acme.sh --issue --dns dns_cf -d domain.com -d "*.domain.com"
```

------

**卸载**

```
acme.sh --uninstall
rm -rf ~/.acme.sh
```

