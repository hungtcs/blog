---
title: C语言结构提占用内存空间的计算方法
categories:
  - C语言
tags:
  - 笔记
  - 结构体
---

### C语言中结构体占用内存空间的计算方法

假设有以下结构体
```c struct.c
struct Type {
  char a;
  int b;
}
struct Type type;
printf("sizeof type is %ld\n", sizeof(type));
// sizeof type is 8
```
我们知道`char`类型占1个字节，`int`类型变量占4个字节，

<table>
  <tr>
    <th>地址</th>
    <th>作用</th>
  </tr>
  <tr>
    <td style="width: 100px;">0x00</td>
    <td>a</td>
  </tr>
  <tr>
    <td>0x01</td>
    <td>空</td>
  </tr>
  <tr>
    <td>0x02</td>
    <td>空</td>
  </tr>
  <tr>
    <td>0x03</td>
    <td>空</td>
  </tr>
  <tr>
    <td>0x04</td>
    <td>b</td>
  </tr>
  <tr>
    <td>0x05</td>
    <td>b</td>
  </tr>
  <tr>
    <td>0x06</td>
    <td>b</td>
  </tr>
  <tr>
    <td>0x07</td>
    <td>b</td>
  </tr>
</table>
