---
title: 26. linux利用socat虚拟串口
date: 2020-06-16 13:33:22
description:
categories:
  - Linux
  - Softwares
tags:
  - linux
  - serial
  - socat
---

Socat 是 Linux 下的一个多功能的网络工具，名字来由是 「Socket CAT」。其功能与有瑞士军刀之称的 Netcat 类似，可以看做是 Netcat 的加强版。

<!-- More -->

### Socat安装
```
$ sudo apt install socat
```

### 创建虚拟串口
```
$ socat -d -d pty,raw,echo=0 pty,raw,echo=0
2020/06/16 13:42:10 socat[6958] N PTY is /dev/pts/2
2020/06/16 13:42:10 socat[6958] N PTY is /dev/pts/3
2020/06/16 13:42:10 socat[6958] N starting data transfer loop with FDs [5,5] and [7,7]
```

现在`/dev/pts/2`和`/dev/pts/3`可以互通了

### 帮助
```
$ sicat -h
socat by Gerhard Rieger and contributors - see www.dest-unreach.org
Usage:
socat [options] <bi-address> <bi-address>
   options:
      -V     print version and feature information to stdout, and exit
      -h|-?  print a help text describing command line options and addresses
      -hh    like -h, plus a list of all common address option names
      -hhh   like -hh, plus a list of all available address option names
      -d     increase verbosity (use up to 4 times; 2 are recommended)
      -D     analyze file descriptors before loop
      -ly[facility]  log to syslog, using facility (default is daemon)
      -lf<logfile>   log to file
      -ls            log to stderr (default if no other log)
      -lm[facility]  mixed log mode (stderr during initialization, then syslog)
      -lp<progname>  set the program name used for logging
      -lu            use microseconds for logging timestamps
      -lh            add hostname to log messages
      -v     verbose data traffic, text
      -x     verbose data traffic, hexadecimal
      -b<size_t>     set data buffer size (8192)
      -s     sloppy (continue on error)
      -t<timeout>    wait seconds before closing second channel
      -T<timeout>    total inactivity timeout in seconds
      -u     unidirectional mode (left to right)
      -U     unidirectional mode (right to left)
      -g     do not check option groups
      -L <lockfile>  try to obtain lock, or fail
      -W <lockfile>  try to obtain lock, or wait
      -4     prefer IPv4 if version is not explicitly specified
      -6     prefer IPv6 if version is not explicitly specified
   bi-address:
      pipe[,<opts>]	groups=FD,FIFO
      <single-address>!!<single-address>
      <single-address>
   single-address:
      <address-head>[,<opts>]
   address-head:
      abstract-client:<filename>	groups=FD,SOCKET,RETRY,UNIX
      abstract-connect:<filename>	groups=FD,SOCKET,RETRY,UNIX
      abstract-listen:<filename>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,UNIX
      abstract-recv:<filename>	groups=FD,SOCKET,RETRY,UNIX
      abstract-recvfrom:<filename>	groups=FD,SOCKET,CHILD,RETRY,UNIX
      abstract-sendto:<filename>	groups=FD,SOCKET,RETRY,UNIX
      create:<filename>	groups=FD,REG,NAMED
      exec:<command-line>	groups=FD,FIFO,SOCKET,EXEC,FORK,TERMIOS,PTY,PARENT,UNIX
      fd:<num>	groups=FD,FIFO,CHR,BLK,REG,SOCKET,TERMIOS,UNIX,IP4,IP6,UDP,TCP,SCTP
      gopen:<filename>	groups=FD,FIFO,CHR,BLK,REG,SOCKET,NAMED,OPEN,TERMIOS,UNIX
      interface:<interface>	groups=FD,SOCKET
      ip-datagram:<host>:<protocol>	groups=FD,SOCKET,RANGE,IP4,IP6
      ip-recv:<protocol>	groups=FD,SOCKET,RANGE,IP4,IP6
      ip-recvfrom:<protocol>	groups=FD,SOCKET,CHILD,RANGE,IP4,IP6
      ip-sendto:<host>:<protocol>	groups=FD,SOCKET,IP4,IP6
      ip4-datagram:<host>:<protocol>	groups=FD,SOCKET,RANGE,IP4
      ip4-recv:<protocol>	groups=FD,SOCKET,RANGE,IP4
      ip4-recvfrom:<protocol>	groups=FD,SOCKET,CHILD,RANGE,IP4
      ip4-sendto:<host>:<protocol>	groups=FD,SOCKET,IP4
      ip6-datagram:<host>:<protocol>	groups=FD,SOCKET,RANGE,IP6
      ip6-recv:<protocol>	groups=FD,SOCKET,RANGE,IP6
      ip6-recvfrom:<protocol>	groups=FD,SOCKET,CHILD,RANGE,IP6
      ip6-sendto:<host>:<protocol>	groups=FD,SOCKET,IP6
      open:<filename>	groups=FD,FIFO,CHR,BLK,REG,NAMED,OPEN,TERMIOS
      openssl:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP4,IP6,TCP,OPENSSL
      openssl-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,RANGE,IP4,IP6,TCP,OPENSSL
      pipe:<filename>	groups=FD,FIFO,NAMED,OPEN
      proxy:<proxy-server>:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP4,IP6,TCP,HTTP
      pty	groups=FD,NAMED,TERMIOS,PTY
      sctp-connect:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP4,IP6,SCTP
      sctp-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,RANGE,IP4,IP6,SCTP
      sctp4-connect:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP4,SCTP
      sctp4-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,RANGE,IP4,SCTP
      sctp6-connect:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP6,SCTP
      sctp6-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,RANGE,IP6,SCTP
      socket-connect:<domain>:<protocol>:<remote-address>	groups=FD,SOCKET,CHILD,RETRY
      socket-datagram:<domain>:<type>:<protocol>:<remote-address>	groups=FD,SOCKET,RANGE
      socket-listen:<domain>:<protocol>:<local-address>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,RANGE
      socket-recv:<domain>:<type>:<protocol>:<local-address>	groups=FD,SOCKET,RANGE
      socket-recvfrom:<domain>:<type>:<protocol>:<local-address>	groups=FD,SOCKET,CHILD,RANGE
      socket-sendto:<domain>:<type>:<protocol>:<remote-address>	groups=FD,SOCKET
      socks4:<socks-server>:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP4,IP6,TCP,SOCKS4
      socks4a:<socks-server>:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP4,IP6,TCP,SOCKS4
      stderr	groups=FD,FIFO,CHR,BLK,REG,SOCKET,TERMIOS,UNIX,IP4,IP6,UDP,TCP,SCTP
      stdin	groups=FD,FIFO,CHR,BLK,REG,SOCKET,TERMIOS,UNIX,IP4,IP6,UDP,TCP,SCTP
      stdio	groups=FD,FIFO,CHR,BLK,REG,SOCKET,TERMIOS,UNIX,IP4,IP6,UDP,TCP,SCTP
      stdout	groups=FD,FIFO,CHR,BLK,REG,SOCKET,TERMIOS,UNIX,IP4,IP6,UDP,TCP,SCTP
      system:<shell-command>	groups=FD,FIFO,SOCKET,EXEC,FORK,TERMIOS,PTY,PARENT,UNIX
      tcp-connect:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP4,IP6,TCP
      tcp-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,RANGE,IP4,IP6,TCP
      tcp4-connect:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP4,TCP
      tcp4-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,RANGE,IP4,TCP
      tcp6-connect:<host>:<port>	groups=FD,SOCKET,CHILD,RETRY,IP6,TCP
      tcp6-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RETRY,RANGE,IP6,TCP
      tun[:<ip-addr>/<bits>]	groups=FD,CHR,NAMED,OPEN,INTERFACE
      udp-connect:<host>:<port>	groups=FD,SOCKET,IP4,IP6,UDP
      udp-datagram:<host>:<port>	groups=FD,SOCKET,RANGE,IP4,IP6,UDP
      udp-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RANGE,IP4,IP6,UDP
      udp-recv:<port>	groups=FD,SOCKET,RANGE,IP4,IP6,UDP
      udp-recvfrom:<port>	groups=FD,SOCKET,CHILD,RANGE,IP4,IP6,UDP
      udp-sendto:<host>:<port>	groups=FD,SOCKET,IP4,IP6,UDP
      udp4-connect:<host>:<port>	groups=FD,SOCKET,IP4,UDP
      udp4-datagram:<remote-address>:<port>	groups=FD,SOCKET,RANGE,IP4,UDP
      udp4-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RANGE,IP4,UDP
      udp4-recv:<port>	groups=FD,SOCKET,RANGE,IP4,UDP
      udp4-recvfrom:<host>:<port>	groups=FD,SOCKET,CHILD,RANGE,IP4,UDP
      udp4-sendto:<host>:<port>	groups=FD,SOCKET,IP4,UDP
      udp6-connect:<host>:<port>	groups=FD,SOCKET,IP6,UDP
      udp6-datagram:<host>:<port>	groups=FD,SOCKET,RANGE,IP6,UDP
      udp6-listen:<port>	groups=FD,SOCKET,LISTEN,CHILD,RANGE,IP6,UDP
      udp6-recv:<port>	groups=FD,SOCKET,RANGE,IP6,UDP
      udp6-recvfrom:<port>	groups=FD,SOCKET,CHILD,RANGE,IP6,UDP
      udp6-sendto:<host>:<port>	groups=FD,SOCKET,IP6,UDP
      unix-client:<filename>	groups=FD,SOCKET,NAMED,RETRY,UNIX
      unix-connect:<filename>	groups=FD,SOCKET,NAMED,RETRY,UNIX
      unix-listen:<filename>	groups=FD,SOCKET,NAMED,LISTEN,CHILD,RETRY,UNIX
      unix-recv:<filename>	groups=FD,SOCKET,NAMED,RETRY,UNIX
      unix-recvfrom:<filename>	groups=FD,SOCKET,NAMED,CHILD,RETRY,UNIX
      unix-sendto:<filename>	groups=FD,SOCKET,NAMED,RETRY,UNIX
```
