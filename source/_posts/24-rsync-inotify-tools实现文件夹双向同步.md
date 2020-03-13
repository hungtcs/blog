---
title: 24. rsync+inotify-tools实现文件夹双向同步
date: 2020-03-13 11:39:55
description:
categories:
  - Linux
tags:
  - rsync
---

基于Ubuntu Linux，适用于大多数Debian发行版。

### 环境
```
操作系统：Ubuntu 18.04
文件夹：
  - /home/hungtcs/Temp/d1
  - /home/hungtcs/Temp/d2
```
<!-- More -->

### 安装
```shell
sudo apt install rsync inotify-tools
```

### rsync主要参数
```
--links             copy symlinks as symlinks
--delete            允许删除文件
--sparse            handle sparse files efficiently
--backup            make backups (see --suffix & --backup-dir)
--backup-dir=DIR    make backups into hierarchy based in DIR
--archive           archive mode; equals -rlptgoD (no -H,-A,-X)
--verbose           显示详细输出
--recursive         递归同步文件夹内容
--human-readable    output numbers in a human-readable format
```

### 同步文件夹`d1`到`d2`
```shell
rsync --verbose --recursive --archive --delete --human-readable --sparse ./d1/ ./d2/
```

### 监听文件更改
```shell
inotifywait --recursive --event=modify,create,delete,move ./d1
```

### 监听文件夹`d1`改动并同步`d2`
```shell
while inotifywait --recursive --event=modify,create,delete,move ./d1; do
  rsync --verbose --recursive --archive --delete --human-readable --sparse ./d1/ ./d2/
done
```

### 监听文件夹`d2`改动并同步到`d1`
```shell
while inotifywait --recursive --event=modify,create,delete,move ./d2; do
  rsync --verbose --recursive --archive --delete --human-readable --sparse ./d2/ ./d1/
done
```
