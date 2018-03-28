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
