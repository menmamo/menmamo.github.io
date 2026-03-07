---
title: "配置iptables"
author: "mona"
date: 2022-01-09T02:09:00+08:00
categories: ["Linux"]
tags: []
---

## 安装iptables

```shell
apt-get update
apt-get install iptables
```

## 放行端口

```shell
iptables -I INPUT -p tcp --dport 443 -j ACCEPT
iptables -I INPUT -p tcp --dport 80 -j ACCEPT
iptables -I INPUT -p tcp --dport 22 -j ACCEPT
iptables -I INPUT -p udp --dport 1024:65535 -j ACCEPT
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -P INPUT DROP
iptables -P FORWARD DROP
```

## 删除规则

```shell
iptables -L -n --line-number
iptables -D INPUT 3
```

## 保存规则

```shell
iptables-save
```

## 持久化规则

### 安装iptables-persistent

```shell
apt install iptables-persistent
```

### 保存规则

```shell
netfilter-persistent save
netfilter-persistent reload
```

## ipv6防火墙

```shell
ip6tables -I INPUT -p tcp --dport 443 -j ACCEPT
ip6tables -I INPUT -p tcp --dport 80 -j ACCEPT
ip6tables -I INPUT -p tcp --dport 22 -j ACCEPT
ip6tables -A INPUT -p icmpv6 -j ACCEPT
ip6tables -I INPUT -p udp --dport 1024:65535 -j ACCEPT
ip6tables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
ip6tables -A INPUT -i lo -j ACCEPT
ip6tables -P INPUT DROP
ip6tables -P FORWARD DROP
```