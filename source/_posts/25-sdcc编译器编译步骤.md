---
title: 25. sdcc编译器编译步骤
date: 2020-05-22 11:49:52
description: sdcc 源码安装
categories:
tags:
  - sdcc
---

1. 下载sdcc源码
    - [http://sdcc.sourceforge.net](http://sdcc.sourceforge.net)
2. 解压
    ```shell
    tar -xjvf sdcc-src-4.0.0.tar.bz2
    cd sdcc-4.0.0
    ```
3. 安装依赖
    ```shell
    sudo apt install flex bison texinfo libboost1.62-dev
    ```
<!-- More -->
4. configure 禁用不必要的芯片支持，只保留mcs51和stm8
    ```shell
    ./configure --disable-z80-port --disable-z180-port --disable-r2k-port --disable-r3ka-port --disable-gbz80-port --disable-tlcs90-port --disable-ez80_z80-port --disable-ds390-port --disable-ds400-port --disable-pic14-port --disable-pic16-port --disable-hc08-port --disable-s08-port --disable-pdk13-port --disable-pdk14-port --disable-pdk15-port
    ```
5. 编译安装
    ```shell
    make
    sudo make install
    ```
