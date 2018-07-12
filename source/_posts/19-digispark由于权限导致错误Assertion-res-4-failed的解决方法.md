---
title: 19. digispark由于权限导致错误Assertion res >= 4 failed的解决方法
date: 2018-07-12 12:58:31
description:
categories:
  - 单片机
  - Arduino
  - Digispark
tags:
  - Arduino
  - Digispark
  - USB
---

### 错误提示
```
micronucleus: library/micronucleus_lib.c:66: micronucleus_connect: Assertion `res >= 4' failed.
```

### 解决方法
创建文件`/etc/udev/rules.d/digispark.rules`，并写入以下内容
```
SUBSYSTEM=="usb", ATTR{idVendor}=="16d0", ATTR{idProduct}=="0753", MODE="0660", GROUP="dialout"
```
确保用户在`dialout`组，否则执行`sudo usermod -aG dialout { username }`
