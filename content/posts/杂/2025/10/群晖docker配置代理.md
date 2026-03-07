---
title: "群晖docker配置代理"
author: "mona"
date: 2025-10-27T22:16:48+08:00
categories: ["杂"]
tags: []
---

**在 SSH 终端执行以下命令：**

```
mkdir -p /etc/systemd/system/pkg-ContainerManager-dockerd.service.d
```

**创建并编辑代理配置文件**

```
vi /etc/systemd/system/pkg-ContainerManager-dockerd.service.d/http-proxy.conf
```

**粘贴如下内容**

```
[Service]
Environment="HTTP_PROXY=socks5://用户名:密码@192.168.1.3:10808"
Environment="HTTPS_PROXY=socks5://用户名:密码@192.168.1.3:10808"
Environment="NO_PROXY=localhost,127.0.0.1"
```

**重新加载并重启 Docker 服务**

```
systemctl daemon-reload
systemctl restart pkg-ContainerManager-dockerd.service
```

**验证代理是否设置成功**

```
systemctl show --property=Environment pkg-ContainerManager-dockerd.service
```

如果显示的内容中有你刚刚设置的代理信息，说明配置成功

```
Environment=HTTP_PROXY=http://192.168.1.3:10808
Environment=HTTPS_PROXY=http://192.168.1.3:10808
Environment=NO_PROXY=localhost,127.0.0.1
```

**恢复设置**

```
rm -f /etc/systemd/system/pkg-ContainerManager-dockerd.service.d/http-proxy.conf
```

```
systemctl daemon-reload
```

```
systemctl restart pkg-ContainerManager-dockerd.service
```

