---
title: 20. Ubuntu/Debian 安装ST-link的过程和方法
date: 2018-07-23 09:45:41
description:
categories:
  - Linux
  - Debian
tags:
  - STM32
  - ST-link
  - Ubuntu
---

### 安装依赖
```
sudo apt install libusb-1.0-0-dev libusb-1.0-0 cmake build-essential
```
<!-- more -->

### 克隆stlink到本地目录
```bash
$ git clone --depth=1 https://github.com/texane/stlink.git
正克隆到 'stlink'...
remote: Counting objects: 175, done.
remote: Compressing objects: 100% (141/141), done.
remote: Total 175 (delta 4), reused 101 (delta 2), pack-reused 0
接收对象中: 100% (175/175), 198.33 KiB | 141.00 KiB/s, 完成.
处理 delta 中: 100% (4/4), 完成.
检查连接... 完成。
$
$ cd stlink
$ ls -al
总用量 116K
drwxrwxr-x 15 hung hung 4.0K 7月  23 09:47 .
drwxr-xr-x  9 hung hung 4.0K 7月  23 09:47 ..
-rw-rw-r--  1 hung hung  593 7月  23 09:47 .appveyor.yml
-rw-rw-r--  1 hung hung 7.2K 7月  23 09:47 ChangeLog.md
drwxrwxr-x  3 hung hung 4.0K 7月  23 09:47 cmake
-rw-rw-r--  1 hung hung 5.8K 7月  23 09:47 CMakeLists.txt
drwxrwxr-x  3 hung hung 4.0K 7月  23 09:47 debian
drwxrwxr-x  4 hung hung 4.0K 7月  23 09:47 doc
drwxrwxr-x  4 hung hung 4.0K 7月  23 09:47 etc
drwxrwxr-x  2 hung hung 4.0K 7月  23 09:47 flashloaders
drwxrwxr-x  8 hung hung 4.0K 7月  23 09:48 .git
drwxrwxr-x  2 hung hung 4.0K 7月  23 09:47 .github
-rw-rw-r--  1 hung hung   20 7月  23 09:47 .gitignore
drwxrwxr-x  3 hung hung 4.0K 7月  23 09:47 include
-rw-rw-r--  1 hung hung 1.7K 7月  23 09:47 LICENSE
-rw-rw-r--  1 hung hung 1.1K 7月  23 09:47 Makefile
-rw-rw-r--  1 hung hung 8.8K 7月  23 09:47 README.md
drwxrwxr-x  2 hung hung 4.0K 7月  23 09:47 scripts
drwxrwxr-x  7 hung hung 4.0K 7月  23 09:47 src
drwxrwxr-x  2 hung hung 4.0K 7月  23 09:47 stlinkv1_macosx_driver
drwxrwxr-x  2 hung hung 4.0K 7月  23 09:47 tests
-rwxrwxr-x  1 hung hung  656 7月  23 09:47 .travis.sh
-rw-rw-r--  1 hung hung  348 7月  23 09:47 .travis.yml
drwxrwxr-x  3 hung hung 4.0K 7月  23 09:47 usr
-rw-rw-r--  1 hung hung    6 7月  23 09:47 .version
```

### 编译stlink
```bash
$ make
-- The C compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- stlink version: 79306cc
--           Major  Minor  Patch
-- Performing Test C_SUPPORTS_STD_GNU99
-- Performing Test C_SUPPORTS_STD_GNU99 - Success
-- Performing Test C_SUPPORTS_WALL
-- Performing Test C_SUPPORTS_WALL - Success
-- Performing Test C_SUPPORTS_WEXTRA
-- Performing Test C_SUPPORTS_WEXTRA - Success
-- Performing Test C_SUPPORTS_WSHADOW
-- Performing Test C_SUPPORTS_WSHADOW - Success
-- Performing Test C_SUPPORTS_D_FORTIFY_SOURCE_2
-- Performing Test C_SUPPORTS_D_FORTIFY_SOURCE_2 - Success
-- Performing Test C_SUPPORTS_FSTRICT_ALIASING
-- Performing Test C_SUPPORTS_FSTRICT_ALIASING - Success
-- Performing Test C_SUPPORTS_WUNDEF
-- Performing Test C_SUPPORTS_WUNDEF - Success
-- Performing Test C_SUPPORTS_WFORMAT
-- Performing Test C_SUPPORTS_WFORMAT - Success
-- Performing Test C_SUPPORTS_WFORMAT_SECURITY
-- Performing Test C_SUPPORTS_WFORMAT_SECURITY - Success
-- Performing Test C_SUPPORTS_WMAYBE_UNINITIALIZED
-- Performing Test C_SUPPORTS_WMAYBE_UNINITIALIZED - Success
-- Performing Test C_SUPPORTS_WMISSING_VARIABLE_DECLARATIONS
-- Performing Test C_SUPPORTS_WMISSING_VARIABLE_DECLARATIONS - Failed
-- Performing Test C_SUPPORTS_WSHORTEN_64_TO_32
-- Performing Test C_SUPPORTS_WSHORTEN_64_TO_32 - Failed
-- Performing Test C_SUPPORTS_WIMPLICIT_FUNCTION_DECLARATION
-- Performing Test C_SUPPORTS_WIMPLICIT_FUNCTION_DECLARATION - Success
-- Performing Test C_SUPPORTS_WREDUNDANT_DECLS
-- Performing Test C_SUPPORTS_WREDUNDANT_DECLS - Success
-- Performing Test C_SUPPORTS_FPIC
-- Performing Test C_SUPPORTS_FPIC - Success
-- Performing Test C_SUPPORTS_O2
-- Performing Test C_SUPPORTS_O2 - Success
-- Performing Test C_SUPPORTS_WERROR
-- Performing Test C_SUPPORTS_WERROR - Success
-- Found Libusb: /usr/lib/x86_64-linux-gnu/libusb-1.0.so
-- found USB
-- Found PkgConfig: /usr/bin/pkg-config (found version "0.29.1")
-- Checking for module 'gtk+-3.0'
--   Found gtk+-3.0, version 3.18.9
-- Looking for sys/mman.h
-- Looking for sys/mman.h - found
-- Looking for unistd.h
-- Looking for unistd.h - found
CMake Error at CMakeLists.txt:121 (set_target_properties):
  set_target_properties called with incorrect number of arguments.


-- Manpage generation disabled
-- Debian-based Linux OS detected
-- Configuring incomplete, errors occurred!
See also "/home/hung/Temp/stlink/build/Release/CMakeFiles/CMakeOutput.log".
See also "/home/hung/Temp/stlink/build/Release/CMakeFiles/CMakeError.log".
Makefile:42: recipe for target 'build/Release' failed
make: *** [build/Release] Error 1
```
上述错误的解决办法是删除`.git`目录
```bash
$ rm -rf .git
$ make clean
$ make
-- The C compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- stlink version: 1.5.0
--           Major 1 Minor 5 Patch 0
-- Performing Test C_SUPPORTS_STD_GNU99
-- Performing Test C_SUPPORTS_STD_GNU99 - Success
-- Performing Test C_SUPPORTS_WALL
-- Performing Test C_SUPPORTS_WALL - Success
-- Performing Test C_SUPPORTS_WEXTRA
-- Performing Test C_SUPPORTS_WEXTRA - Success
-- Performing Test C_SUPPORTS_WSHADOW
-- Performing Test C_SUPPORTS_WSHADOW - Success
-- Performing Test C_SUPPORTS_D_FORTIFY_SOURCE_2
-- Performing Test C_SUPPORTS_D_FORTIFY_SOURCE_2 - Success
-- Performing Test C_SUPPORTS_FSTRICT_ALIASING
-- Performing Test C_SUPPORTS_FSTRICT_ALIASING - Success
-- Performing Test C_SUPPORTS_WUNDEF
-- Performing Test C_SUPPORTS_WUNDEF - Success
-- Performing Test C_SUPPORTS_WFORMAT
-- Performing Test C_SUPPORTS_WFORMAT - Success
-- Performing Test C_SUPPORTS_WFORMAT_SECURITY
-- Performing Test C_SUPPORTS_WFORMAT_SECURITY - Success
-- Performing Test C_SUPPORTS_WMAYBE_UNINITIALIZED
-- Performing Test C_SUPPORTS_WMAYBE_UNINITIALIZED - Success
-- Performing Test C_SUPPORTS_WMISSING_VARIABLE_DECLARATIONS
-- Performing Test C_SUPPORTS_WMISSING_VARIABLE_DECLARATIONS - Failed
-- Performing Test C_SUPPORTS_WSHORTEN_64_TO_32
-- Performing Test C_SUPPORTS_WSHORTEN_64_TO_32 - Failed
-- Performing Test C_SUPPORTS_WIMPLICIT_FUNCTION_DECLARATION
-- Performing Test C_SUPPORTS_WIMPLICIT_FUNCTION_DECLARATION - Success
-- Performing Test C_SUPPORTS_WREDUNDANT_DECLS
-- Performing Test C_SUPPORTS_WREDUNDANT_DECLS - Success
-- Performing Test C_SUPPORTS_FPIC
-- Performing Test C_SUPPORTS_FPIC - Success
-- Performing Test C_SUPPORTS_O2
-- Performing Test C_SUPPORTS_O2 - Success
-- Performing Test C_SUPPORTS_WERROR
-- Performing Test C_SUPPORTS_WERROR - Success
-- Found Libusb: /usr/lib/x86_64-linux-gnu/libusb-1.0.so
-- found USB
-- Found PkgConfig: /usr/bin/pkg-config (found version "0.29.1")
-- Checking for module 'gtk+-3.0'
--   Found gtk+-3.0, version 3.18.9
-- Looking for sys/mman.h
-- Looking for sys/mman.h - found
-- Looking for unistd.h
-- Looking for unistd.h - found
-- Manpage generation disabled
-- Debian-based Linux OS detected
-- Configuring done
-- Generating done
-- Build files have been written to: /home/hung/Temp/stlink/build/Release
[RELEASE]
Scanning dependencies of target stlink-static
[  2%] Building C object CMakeFiles/stlink-static.dir/src/chipid.c.o
[  5%] Building C object CMakeFiles/stlink-static.dir/src/common.c.o
[  8%] Building C object CMakeFiles/stlink-static.dir/src/usb.c.o
[ 11%] Building C object CMakeFiles/stlink-static.dir/src/sg.c.o
[ 14%] Building C object CMakeFiles/stlink-static.dir/src/logging.c.o
[ 17%] Building C object CMakeFiles/stlink-static.dir/src/flash_loader.c.o
[ 20%] Linking C static library libstlink.a
[ 20%] Built target stlink-static
Scanning dependencies of target stlink
[ 23%] Building C object CMakeFiles/stlink.dir/src/chipid.c.o
[ 26%] Building C object CMakeFiles/stlink.dir/src/common.c.o
[ 29%] Building C object CMakeFiles/stlink.dir/src/usb.c.o
[ 32%] Building C object CMakeFiles/stlink.dir/src/sg.c.o
[ 35%] Building C object CMakeFiles/stlink.dir/src/logging.c.o
[ 38%] Building C object CMakeFiles/stlink.dir/src/flash_loader.c.o
[ 41%] Linking C shared library libstlink.so
[ 41%] Built target stlink
Scanning dependencies of target st-flash
[ 44%] Building C object CMakeFiles/st-flash.dir/src/tools/flash.c.o
[ 47%] Building C object CMakeFiles/st-flash.dir/src/tools/flash_opts.c.o
[ 50%] Linking C executable st-flash
[ 50%] Built target st-flash
Scanning dependencies of target st-info
[ 52%] Building C object CMakeFiles/st-info.dir/src/tools/info.c.o
[ 55%] Linking C executable st-info
[ 55%] Built target st-info
Scanning dependencies of target st-util
[ 58%] Building C object src/gdbserver/CMakeFiles/st-util.dir/gdb-remote.c.o
[ 61%] Building C object src/gdbserver/CMakeFiles/st-util.dir/gdb-server.c.o
[ 64%] Building C object src/gdbserver/CMakeFiles/st-util.dir/semihosting.c.o
[ 67%] Linking C executable st-util
[ 67%] Built target st-util
Scanning dependencies of target stlink-gui-local
[ 70%] Building C object src/tools/gui/CMakeFiles/stlink-gui-local.dir/stlink-gui.c.o
[ 73%] Linking C executable stlink-gui-local
[ 73%] Built target stlink-gui-local
Scanning dependencies of target stlink-gui
[ 76%] Building C object src/tools/gui/CMakeFiles/stlink-gui.dir/stlink-gui.c.o
[ 79%] Linking C executable stlink-gui
[ 79%] Built target stlink-gui
Scanning dependencies of target sg
[ 82%] Building C object tests/CMakeFiles/sg.dir/sg.c.o
[ 85%] Linking C executable sg
[ 85%] Built target sg
Scanning dependencies of target usb
[ 88%] Building C object tests/CMakeFiles/usb.dir/usb.c.o
[ 91%] Linking C executable usb
[ 91%] Built target usb
Scanning dependencies of target flash
[ 94%] Building C object tests/CMakeFiles/flash.dir/flash.c.o
[ 97%] Building C object tests/CMakeFiles/flash.dir/__/src/tools/flash_opts.c.o
[100%] Linking C executable flash
[100%] Built target flash
```

### 安装stlink
```bash
$ cd build/Release
$ ls -al
总用量 352
drwxrwxr-x 8 hung hung   4096 7月  23 09:52 .
drwxrwxr-x 3 hung hung   4096 7月  23 09:51 ..
-rw-rw-r-- 1 hung hung  19316 7月  23 09:51 CMakeCache.txt
drwxrwxr-x 8 hung hung   4096 7月  23 09:52 CMakeFiles
-rw-rw-r-- 1 hung hung   7490 7月  23 09:51 cmake_install.cmake
-rw-r--r-- 1 hung hung   3447 7月  23 09:51 CPackConfig.cmake
-rw-r--r-- 1 hung hung   3850 7月  23 09:51 CPackSourceConfig.cmake
drwxrwxr-x 3 hung hung   4096 7月  23 09:51 doc
drwxrwxr-x 4 hung hung   4096 7月  23 09:51 include
-rw-rw-r-- 1 hung hung 132872 7月  23 09:52 libstlink.a
lrwxrwxrwx 1 hung hung     14 7月  23 09:52 libstlink.so -> libstlink.so.1
lrwxrwxrwx 1 hung hung     18 7月  23 09:52 libstlink.so.1 -> libstlink.so.1.5.0
-rwxrwxr-x 1 hung hung  96312 7月  23 09:52 libstlink.so.1.5.0
-rw-rw-r-- 1 hung hung  18977 7月  23 09:51 Makefile
drwxrwxr-x 4 hung hung   4096 7月  23 09:51 src
-rwxrwxr-x 1 hung hung  18528 7月  23 09:52 st-flash
-rwxrwxr-x 1 hung hung  13480 7月  23 09:52 st-info
drwxrwxr-x 3 hung hung   4096 7月  23 09:52 tests
drwxrwxr-x 3 hung hung   4096 7月  23 09:51 usr
$ make
[ 20%] Built target stlink-static
[ 41%] Built target stlink
[ 50%] Built target st-flash
[ 55%] Built target st-info
[ 67%] Built target st-util
[ 73%] Built target stlink-gui-local
[ 79%] Built target stlink-gui
[ 85%] Built target sg
[ 91%] Built target usb
[100%] Built target flash
$ sudo make install
[sudo] hung 的密码：
[ 20%] Built target stlink-static
[ 41%] Built target stlink
[ 50%] Built target st-flash
[ 55%] Built target st-info
[ 67%] Built target st-util
[ 73%] Built target stlink-gui-local
[ 79%] Built target stlink-gui
[ 85%] Built target sg
[ 91%] Built target usb
[100%] Built target flash
Install the project...
-- Install configuration: "Release"
-- Installing: /usr/local/lib/libstlink.so.1.5.0
-- Up-to-date: /usr/local/lib/libstlink.so.1
-- Up-to-date: /usr/local/lib/libstlink.so
-- Installing: /usr/local/lib/libstlink.a
-- Installing: /usr/local/bin/st-flash
-- Set runtime path of "/usr/local/bin/st-flash" to ""
-- Installing: /usr/local/bin/st-info
-- Set runtime path of "/usr/local/bin/st-info" to ""
-- Installing: /etc/modprobe.d/stlink_v1.conf
-- Installing: /etc/udev/rules.d/49-stlinkv1.rules
-- Installing: /etc/udev/rules.d/49-stlinkv2-1.rules
-- Installing: /etc/udev/rules.d/49-stlinkv2.rules
-- Installing: /usr/local/bin/st-util
-- Set runtime path of "/usr/local/bin/st-util" to ""
-- Installing: /usr/local/bin/stlink-gui
-- Installing: /usr/local/share/stlink/stlink-gui.ui
-- Installing: /usr/local/share/applications/stlink-gui.desktop
-- Installing: /usr/local/share/icons/hicolor/scalable/apps/stlink-gui.svg
-- Installing: /usr/local/lib/pkgconfig/stlink.pc
-- Installing: /usr/local/include/stlink.h
-- Installing: /usr/local/include/stlink/commands.h
-- Installing: /usr/local/include/stlink/flash_loader.h
-- Installing: /usr/local/include/stlink/mmap.h
-- Installing: /usr/local/include/stlink/sg.h
-- Installing: /usr/local/include/stlink/usb.h
-- Installing: /usr/local/include/stlink/backend.h
-- Installing: /usr/local/include/stlink/reg.h
-- Installing: /usr/local/include/stlink/chipid.h
-- Installing: /usr/local/include/stlink/logging.h
-- Installing: /usr/local/include/stlink/version.h
-- Installing: /usr/local/share/man/man1/st-util.1
-- Installing: /usr/local/share/man/man1/st-flash.1
-- Installing: /usr/local/share/man/man1/st-info.1
$ which st-flash
/usr/local/bin/st-flash
$ st-flash --version
st-flash: error while loading shared libraries: libstlink.so.1: cannot open shared object file: No such file or directory
$ ldd /usr/local/bin/st-flash
    linux-vdso.so.1 =>  (0x00007fffc0ad0000)
    libstlink.so.1 => not found
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f68856c0000)
    libusb-1.0.so.0 => /lib/x86_64-linux-gnu/libusb-1.0.so.0 (0x00007f68854a8000)
    /lib64/ld-linux-x86-64.so.2 (0x00007f6885c9f000)
    libudev.so.1 => /lib/x86_64-linux-gnu/libudev.so.1 (0x00007f6885e7a000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f688528b000)
    librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f6885083000)
```
如果遇到上述错误，可以执行`sudo ldconfig`解决
```bash
$ ldd /usr/local/bin/st-flash
    linux-vdso.so.1 =>  (0x00007ffdb1bdb000)
    libstlink.so.1 => /usr/local/lib/libstlink.so.1 (0x00007f4580cf6000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f458092c000)
    libusb-1.0.so.0 => /lib/x86_64-linux-gnu/libusb-1.0.so.0 (0x00007f4580714000)
    /lib64/ld-linux-x86-64.so.2 (0x00007f4580f0b000)
    libudev.so.1 => /lib/x86_64-linux-gnu/libudev.so.1 (0x00007f45810e6000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f45804f7000)
    librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f45802ef000)
$ st-flash --version
v1.5.0
```
