---
title: "ssh配置密钥登陆"
author: "mona"
date: 2023-01-16T20:16:00+08:00
categories: ["Linux"]
tags: []
---

##### 修改默认端口

```shell
nano /etc/ssh/sshd_config
```

```shell
#修改默认端口
Prot 25428
```

##### 新建非root用户，禁用root登陆

```shell
adduser username
apt update && apt install sudo
visudo
```

```shell
#在 User Privilege Specification 下加入一行 username ALL=(ALL) NOPASSWD: ALL
username ALL=(ALL) NOPASSWD: ALL
```

```shell
nano /etc/ssh/sshd_config
#PermitRootLogin Yes改为no
PermitRootLogin no
systemctl restart ssh
```

##### 使用密钥登陆，关闭密码登陆

运行`PuTTYgen`

![img](/usr/uploads/2023/01/ch04-img08-puttygen-save.21bcf24c.png)





点击 `Save public key` 保存公钥，文件名为 `id_rsa.pub`

点击 `Save private key` 保存私钥，文件名为 `id_rsa` (PuTTY 私钥自带`.ppk`后缀)

将上方红框内的内容，向下滚动全部复制出来并保存，文件名为 `authorized_keys`

```shell
su username
cd /home/username/
mkdir .ssh
//将红框内容复制进去
vi authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

```shell
sudo vi /etc/ssh/sshd_config
PasswordAuthentication no
PubkeyAuthentication  yes
sudo systemctl restart ssh
```

###### putty设置密钥登陆



![img](/usr/uploads/2023/01/ch04-img18-putty-privatekey-location.6c472eca.png)

[参考](https://xtls.github.io/document/level-0/ch04-security.html#_4-7-使用-rsa-密钥登录并禁用密码登录)