---
title: 17. 如何在Linux上使用STM8单片机
categories:
  - 单片机
  - stm8
tags:
  - stm8
date: 2018-05-24 16:25:17
---


在上一篇文章中，我们看了一下廉价的STM8开发板和编程使用的ST-Link。现在是时候采取一些行动了！

### 环境配置

#### 实例程序和文档

先阅读一些资料是一个良好的开端。

ST提供大量关于[STM8S103F3]的免费资料，[该页面][STM8S103F3]还列出了微控制器的所有主要功能，如果你想看的话。

如果你按照我的步骤开始的话，我推荐你另外阅读一下[SDCC的用户手册]，以便了了解SDCC编译器的特殊语法。

<!-- more -->

#### 编程软件

**本指南是为Arch Linux编写的，其他发行版的用户可能会发现某些指令不能运行，请根据需要调整**

在Linux上开发STM8单片机需要下面两个软件的配合：

- [stm8flash] 一个用于链接ST-Link的软件适配器
- [sdcc] 一个编译器

ST还提供了一些其他为STM8编程的[编译器或工具]，
但是他们只可以运行在windows上，或许你可以通过wine软件来运行，但是这都不是一个好办法。

_如果你还打算使用ST-Link对STM32进行开发，你应该还需要安装[community/stlink](https://www.archlinux.org/packages/?q=stlink)_

您可能需要也可能不需要以下内容`/etc/udev/rules.d/49-stlinkv2.rules`，
我忘记了这是从哪里复制回来的了

```
# stm32 discovery boards, with onboard st/linkv2
# ie, STM32L, STM32F4.
# STM32VL has st/linkv1, which is quite different

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", \
    MODE:="0666", \
    SYMLINK+="stlinkv2_%n"

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", \
    KERNEL!="sd*", KERNEL!="sg*", KERNEL!="tty*", SUBSYSTEM!="bsg", \
    MODE:="0666", \
    SYMLINK+="stlinkv2_%n"

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", \
    KERNEL=="sd*", MODE:="0666", \
    SYMLINK+="stlinkv2_disk"

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", \
    KERNEL=="sg*", MODE:="0666", \
    SYMLINK+="stlinkv2_raw_scsi"

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", \
    SUBSYSTEM=="bsg", MODE:="0666", \
    SYMLINK+="stlinkv2_block_scsi"

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", \
    KERNEL=="tty*", MODE:="0666", \
    SYMLINK+="stlinkv2_console"

# If you share your linux system with other users, or just don't like the
# idea of write permission for everybody, you can replace MODE:="0666" with
# OWNER:="yourusername" to create the device owned by you, or with
# GROUP:="somegroupname" and control access using standard unix groups.
```

#### 库文件

SDCC支持STM8，但由于许可原因(booo, ST!)，标准外设库（SPL）是缺失的。

有人开发了一个使SPL与SDCC兼容的补丁，[点击这里](https://github.com/gicking/SPL_2.2.0_SDCC_patch)
(这个补丁已经过期了，[点击这里查看另一个更新的](https://github.com/gicking/STM8-SPL_SDCC_patch))，
另外有一个AUR软件包试([aur/stm8-spl-sdcc](https://aur.archlinux.org/packages/stm8-spl-sdcc/))图将其安装在SDCC库文件夹中,
但是这些SPL的文件包具有登录和EULA点击保护(but alas the zip with the SPL files is login & EULA-click protected)(booo again, ST!)。

构建这个软件包的任务非常艰巨！

##### 下载SPL

首先，你必须从ST的官网(the evil corporate's claws)下载SPL包的zip文件。
[点击这里](https://my.st.com/content/my_st_com/en/products/embedded-software/mcus-embedded-software/stm8-embedded-software/stsw-stm8069.html)

我为此做了一个图片向导：
{% asset_img 01.jpg %}
{% asset_img 02.jpg %}
{% asset_img 03.jpg %}
{% asset_img 04.jpg %}
{% asset_img 05.jpg %}
{% asset_img 06.jpg %}

##### 打补丁

在你下载了SPL的压缩包之后，为了让他能在SDCC下使用，我们需要给它打补丁，因为它是为非自由编译器（如Cosmic）制作的。
虽然通过电子邮件的请求可能可以免费获取Cosmic，但是它只能在windows上使用。

让我们开始打补丁吧：

1. 下载AUR软件包，可能使用`cower -d stm8-spl-sdcc`
2. 将来之不易的`en.stsw-stm8069.zip`放到PKGBUILD同级，然后重新命名为`stsw-stm8069.zip`，HASH应该匹配
3. 先运行`mapekpg`，接下来再运行`sudo pacman -U <the xz file>`

非Arch用户可以手动应用这些修补程序

如果你不喜欢在系统目录中安装这样的东西，你可以简单地从`src/STM8S_StdPeriph_Lib`复制修补后的SPL，
然后放到你认为合适的地方（或者直接将`libraries/STM8S_StdPeriph_Driver`放到你的项目中去）。

您还可以从`src/STM8S_StdPeriph_Lib/Project/STM8S_StdPeriph_Template/SDCC`中获取示例Makefile，
虽然需要一些调整，但是还是有一些参考价值。

### 开始你的第一个项目

在此之前，你应该已经准备好工作环境，并且有一个最小系统开发板。

你可以点击这个链接下载[实例程序]，或者直接下载已经[编译好的ihx文件]，前提是你有和我一样的开发板，否则你可能不能直接使用。

接下来调整`makefile`里面的SPL路径，然后运行`make`命令

```
$ make
sdcc -mstm8 -lstm8 --opt-code-size -DSTM8S103 -I./src -I/usr/share/sdcc/include/stm8/  -DSKIP_TRAPS=0 -c ./src/stm8s_it.c -o STM8S103/stm8s_it.rel
sdcc -mstm8 -lstm8 --opt-code-size -DSTM8S103 -I./src -I/usr/share/sdcc/include/stm8/  -DSKIP_TRAPS=0 -c ./src/main.c -o STM8S103/main.rel
sdcc -mstm8 -lstm8 --opt-code-size -DSTM8S103 -I./src -I/usr/share/sdcc/include/stm8/  -DSKIP_TRAPS=0 -c /usr/share/sdcc/lib/src/stm8/stm8s_gpio.c -o STM8S103/stm8s_gpio.rel
sdcc -mstm8 -lstm8 --opt-code-size -o ./STM8S103/STM8S103.hex STM8S103/stm8s_it.rel STM8S103/main.rel STM8S103/stm8s_gpio.rel
```

之所以makefile里面定一个多个步骤，是因为sdcc无法同时编译多文件。
首先我们需要将每个`.c`文件编译到`.rel`文件(类似于`.o`文件，不过这是sdcc的版本),
然后我们再通过这些`.rel`文件生成二进制文件。

#### 组织化你的代码

目前我们的源文件看起来很混乱，大部分原因是因为过多的注释，但是幸好`main.c`文件里面还算清晰，
我们的项目基于ST提供的原始项目模板，这也是大部分注释的来源。同样的，STM32也有类似的问题，
你可以随意清理。

你也可以将SPL的库文件直接复制到你的项目中，然后进行整理--删除里面的废话。
(you should be able to simply #define it as no-op in stm8s.h,
then you save some FLASH bytes by removing the error messages)

现在你可以直接阅读SPL的源代码了，以便从中获取更多你感兴趣的内容。
这些文件通过外设的类型分组，像SPI、IIC、UART等等。

此外，SPL的库文件夹中还有一个大小为6M的chm说明文档，如果你找到办法打开它，它会很有用的。

**如果你选择使用SDCC，你可能会因为它缺少`-Wl,--gc-sections`等功能而不愉快。
它总是将整个库文件与代码捆绑在一起，尽管某些函数显然未被使用。
作为一个解决小文件大小的方法，我把几乎所有的SPL转换成内联函数，
转换过后的代码可以在这里[下载](https://github.com/MightyPork/stm8s_inline_spl)，
不过我只做了简单的测试。**

### 烧写程序

每个最小系统板在出场之前，都会预先安装一个blink闪烁程序，
所以你可以直接插上电源就可以看到它开始工作。

现在你可以链接你的ST-Link开始烧写程序了，
**不要在使用USB供电的时候同时接通ST-LINK的3.3v到开发板，这有可能会烧坏你的烧写器**

烧写的第一步是关闭写保护，因为出厂加载的固件是读保护的，在锁定时你无法做任何事情。
要解锁芯片，请执行下面的命令(运行`stm8flash -h`查看帮助)

```
stm8flash -c stlinkv2 -p stm8s103f3 -u
Determine OPT area
Unlocked device. Option bytes reset to default state.
Bytes written: 11
```

板子此时应该会停止闪烁，不好，你现在把它变成砖了，
不过不用担心，我们马上为它烧写新的程序。

接下来执行`make flash`命令开始烧写你的程序

```
$ stm8flash -c stlinkv2 -p stm8s103f3 -s flash -w stm8_blinky.hex
Determine FLASH area
Writing Intel hex file 655 bytes at 0x8000... OK
Bytes written: 655
```

此时开发板会立即开始闪烁，你可以通过更改演示观察执行效果，OK，It's your time now!

### 最后的想法

真心希望这两篇关于STM8的介绍可以帮助到你！
你现在已经学会的搭建环境和烧写程序，现在尽情发挥你的创意吧！

我打算花一些时间玩STM8单片机，后续可能还会发表一些自己的想法， Until then, happy hacking!

### 声明

这篇文章是译文，原文地址[点击这里][原文地址]，原作者是`Ondřej Hruška`。
此译文已获取原作者邮件授权，请仅用于学习用途，不允许转载，谢谢合作！


[原文地址]: https://www.ondrovo.com/a/20170107-stm8-getting-started/
[STM8S103F3]: http://www.st.com/en/microcontrollers/stm8s103f3.html
[实例程序]: https://www.ondrovo.com/a/20170107-stm8-getting-started/stm8_blinky.zip
[编译好的ihx文件]: https://www.ondrovo.com/a/20170107-stm8-getting-started/stm8_blinky.hex
[编译器或工具]: http://www.st.com/en/development-tools/stm8-software-development-tools.html
[stm8flash]: https://github.com/vdudouyt/stm8flash
[sdcc]: http://sdcc.sourceforge.net/
[SDCC的用户手册]: http://sdcc.sourceforge.net/doc/sdccman.pdf
