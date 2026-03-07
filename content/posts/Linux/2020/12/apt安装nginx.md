---
title: "apt安装nginx"
author: "mona"
date: 2020-12-21T00:22:00+08:00
categories: ["Linux"]
tags: []
---

添加安装源

```shell
nano /etc/apt/sources.list
```

加入两行

```shell
deb http://nginx.org/packages/debian/ bullseye nginx
deb-src http://nginx.org/packages/debian/ bullseye nginx
```

导入key

```shell
wget http://nginx.org/keys/nginx_signing.key
apt-key add nginx_signing.key
```
更新apt包

```shell
apt update -y
```

安装nginx

```shell
apt install nginx
```

重新安装nginx

```shell
sudo apt remove nginx 
sudo apt purge nginx  
rm -rf /etc/nginx
sudo apt autoremove 
apt -o DPkg::options::=--force-confmiss --reinstall install nginx
```

