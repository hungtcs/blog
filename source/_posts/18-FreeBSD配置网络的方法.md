---
title: 18. FreeBSD配置网络的方法
description: 简述配置freebsd网络的方法
date: 2018-06-04 15:22:50
categories:
  - BSD
  - FreeBSD
  - 笔记
tags:
  - FreeBSD
  - 网络配置
---


### 配置IP
1. 编辑`/etc/rc.conf`文件
```sh /etc/rc.conf
hostname="freebsd"
ifconfig_em0="inet 192.168.1.201 netmask 255.255.255.0"
defaultrouter="192.168.1.1"
sshd_enable="YES"

# Set dumpdev to "AUTO" to enable crach dumps, "NO" to disbale
dumpdev="AUTO"
```
2. 执行`reboot`重启或者执行`sh /etc/rc.conf`生效

### 配置DNS服务器
1. 编辑`/etc/resolv.conf`
2. 填写或更改一下内容
```sh /etc/resolv.conf
nameserver 192.168.1.1
```
