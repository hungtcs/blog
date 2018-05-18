---
title: 14. 在linux下给51单片机写程序
date: 2018-05-19 00:10:46
categories:
  - 单片机
  - 51单片机
tags:
  - linux
  - sdcc
  - stcflash
---

### 需要使用到的软件
- sdcc
  > [SDCC] (Small Device C Compiler)是一个优化的嵌入式ANSI C编译器，它支持的平台包括是英特尔[8051]、美信80DS390、Zilog Z80、摩托罗拉68HC08和微芯的PIC16及PIC18系列。
  > [SDCC]是自由开放源码软件，依照GNU通用公共许可证（GPL）分发。
  > 具体支持的芯片列表: [mcs51][8051]、z80、z180、r2k、r3ka、gbz80、tlcs90、ds390、pic16、pic14、TININative、ds400、hc08、s08、[stm8]
- [stcflash]
  > 使用python编写的一个开源跨平台的stc单片机烧写工具

### 步骤
1. 安装sdcc与stcflash
  sdcc可以使用包管理器安装或通过源码安装。
  ```shell
  sudo apt install sdcc
  ```
  stcflash可以中github直接下载到本地，加入环境变量即可。
  ```shell
  wget https://github.com/laborer/stcflash/archive/master.zip
  unzip stcflash-master.zip
  cd stcflash-master
  mv stcflash.py stcflash
  sudo mv stcflash /usr/local/bin
  ```
  然后安装stcflash的依赖包
  ```shell
  sudo apt install python-serial
  ```

2. 使用sdcc风格的C语言编写程序，例如
  ```c
  #include <mcs51/8051.h>

  void main()
  {
    __bit status = 1;
    while(1)
    {
      P0_0 = status;
      status = !status;
      delay(1000);
    }
  }
  ```
  需要注意的是:
  - sdcc中所有非C标准关键字都以两个下划线开头
  - 单个引脚的表示使用下划线表示，例如`P3_2`，与keil的`^`不同
  - 引用的头文件为`mcs51/8051.h`

3. 编译程序和生成hex文件
  ```shell
  sdcc main.c -o main.ihx
  ```
  sdcc编译之后直接得到的是ihx文件，并不是51单片机需要的hex文件，所以需要使用packihx命令打包。
  packihx命令是在安装sdcc的时候附带安装的，所以可以直接使用。
  ```shell
  packihx main.ihx > main.hex
  ```

4. 烧写程序到单片机
  ```
  sudo stcflash --port /dev/ttyUSB0 main.hex
  ```
  烧写的时候需要重新给单片机上电

[8051]: https://zh.wikipedia.org/zh-hans/%E8%8B%B1%E7%89%B9%E7%88%BE8051
[SDCC]: http://sdcc.sourceforge.net/
[stm8]: https://en.wikipedia.org/wiki/STM8
[stcflash]: https://github.com/laborer/stcflash
