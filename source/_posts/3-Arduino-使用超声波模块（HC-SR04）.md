---
title: 3. Arduino 使用超声波模块（HC-SR04）
date: 2018-03-28 22:18:19
categories:
  - Arduino
tags:
  - Arduino
  - Arduino Uno
  - 超声波模块
---

### 接线图
{% asset_img 1.png 接线图 %}

<!-- more -->

### 基本工作原理
1. 采用IO口TRIG触发测距，给至少10us的高电平信号
2. 模块自动发送8个40khz的方波，自动检测是否有信号返回
3. 有信号返回，通过IO口ECHO输出一个高电平，高电平持续的时间就是超声波从发射到返回的时间

{% cq %} 测试距离 = (高电平时间 * 声速(340M/S)) / 2 {% endcq %}

### 代码
```cpp
const int trigPin = 7;
const int echoPin = 8;

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.begin(9600);
}

void loop() {
  long duration, distance;

  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigPin, HIGH);
  delayMicroseconds(40);
  digitalWrite(trigPin, LOW);

  // 读取echoPin，以微秒为单位返回高电平持续时间
  duration = pulseIn(echoPin, HIGH);
  distance= duration * 0.034 / 2;

  Serial.print("Distance: ");
  Serial.println(distance);
}
```

### 串口输出
{% asset_img 2.png 接线图 %}
