---
title: 8. 51单片机使用PCF8591(ADC/DAC)的方法(AD篇)
categories:
  - 单片机
  - 51单片机
tags:
  - PCF8591
  - AD
  - DA
  - 模数转换
date: 2018-04-07 20:52:08
---


### PCF8591介绍
> PCF8591 是具有 I2C 总线接口的 8 位 A/D 及 D/A 转换器。
> 有 4 路 A/D 转换输入,1 路 D/A 模拟输出。
> 这就是说,它既可以作 A/D 转换也可以作 D/A 转换。
> A/D 转换为逐次比较型。
> 电源电压典型值为 5V。

<!-- more -->
> I2C 总线是 Philips 公司推出的新型单片机系统。
> 它采用串行总线,主控器与外围器件仅靠两条线进行信息传输,一条称为时钟线(SCL),另一条位数据线(SDA)。
> I2C 总线单片机系统较通用单片机系统电路简单。
> 由普通 CPU 芯片同 I2C 专用器件组成的系统为模拟 I2C 系统,它性能稳定,价格较低,目前已得到广泛应用。

{% asset_link PCF8591.pdf 文档链接 %}

### PCF8591引脚
{% asset_img 1.png %}


### 代码
```c
// file: main.c
#include <8051.h>
#include <stdio.h>
#include "delay.h"
#include "iic.h"

void setup()
{
  // 定时器工作模式设置
  // 定时器0为工作模式1 用于计时
  // 定时器1为工作模式2 用于串口
  TMOD = 0x20;
  // 设置串口为模式1
  // SM0 0
  // SM1 1
  SCON |= 0x50;
  // 设置定时器1初值和自动装载值
  // 波特率 9600
  TH1 = 0XFD;
  TL1 = 0XFD;
  // 启动定时器1
  TR1=1;
  //中断允许
  EA=1;

  SDA = 1;
  SCL = 1;
}

void loop()
{
  unsigned char data;
  iic_start();
  iic_send_byte(0x90);
  iic_send_byte(0x04);

  iic_start();
  iic_send_byte(0x91);

  iic_receive_byte();
  iic_ack(0);

  data = iic_receive_byte();
  printf("value 1: %d  ", data);
  iic_ack(0);

  data = iic_receive_byte();
  printf("value 2: %d  ", data);
  iic_ack(0);

  data = iic_receive_byte();
  printf("value 3: %d  ", data);
  iic_ack(0);

  data = iic_receive_byte();
  printf("value 4: %d\n", data);
  iic_ack(1);

  iic_stop();
  delay(100);
}

void main()
{
  setup();
  while(1) loop();
}

void putchar(char c)
{
  SBUF = c;
  while(!TI);
  TI = 0;
}
```

```c
// iic.h
#include <8051.h>

#ifndef __IIC_H__
#define __IIC_H__

#define SDA P1_2
#define SCL P1_7

#define __nop __asm nop __endasm

extern void iic_start();
extern void iic_stop();
extern void iic_ack(__bit ack);
extern __bit iic_send_byte(unsigned char data);
extern unsigned char iic_receive_byte();

#endif  // __IIC_H__
```

```c
// file: iic.c
#include "iic.h"

void iic_start()
{
  SDA = 1;
  SCL = 1;
  __nop; __nop; __nop; __nop; __nop;
  SDA = 0;
  __nop; __nop; __nop; __nop; __nop;
  SCL = 0;
}

void iic_stop()
{
  SDA = 0;
  SCL = 1;
  __nop; __nop; __nop; __nop; __nop;
  SDA = 1;
  __nop; __nop; __nop; __nop; __nop;
  // SCL = 0;
}

void iic_ack(__bit ack)
{
  SDA = ack;
  SCL = 1;
  __nop; __nop; __nop; __nop; __nop;
  SCL = 0;
}

__bit iic_send_byte(unsigned char data)
{
  __bit ack;
  unsigned char i;
  SCL = 0;
  for(i=0; i<8; i++)
  {
    SDA = data & 0x80;
    data <<= 1;
    SCL = 1;
    __nop; __nop; __nop; __nop; __nop;
    SCL = 0;
  }
  SDA = 1;
  SCL = 1;
  __nop; __nop; __nop; __nop; __nop;
  ack = SDA;
  SCL = 0;
  return ack;
}

unsigned char iic_receive_byte()
{
  unsigned char i, data = 0;
  for(i=0; i<8; i++)
  {
    SDA = 1;
    SCL = 1;
    data <<= 1;
    if(SDA == 1) {
      data |= 0x01;
    }
    SCL = 0;
  }
  return data;
}
```

```c
// file: delay.h
#ifndef __DELAY_H__
#define __DELAY_H__

void delay(unsigned int time)
{
  unsigned char i, j;
  while(time--)
  {
    for(i=3; i>0; i--)
      for(j=100; j>0; j--);
  }
}

#endif  // __DELAY_H__
```
