---
title: 11. 如何使用esp-01s继电器模块 和 我的WIFI控制电灯整体解决方案
categories:
  - 单片机
  - esp8266
tags:
  - esp-01s
  - 智能家居
  - WIFI Module
date: 2018-05-08 16:23:45
---


### 介绍
如何用WIFI控制家里的电灯？最简单的方法是使用ESP-01s和配套的relay模块。
{% asset_img esp-01s.webp %}
<!-- more -->
{% asset_img relay.webp %}

### 配置Arduino IDE使其支持esp8266芯片
1. 打开 `文件/首选项/附加开发板管理器网址`，添加下面的URL
  ```
  http://arduino.esp8266.com/stable/package_esp8266com_index.json
  ```
2. 打开工具/开发板/开发板管理器，安装esp8266开发板
  {% asset_img 0.png %}

Github: [esp8266/arduino](https://github.com/esp8266/arduino)

### 需要使用的工具
- `usb-ttl`一个 (须支持3.3v)
- 杜邦线若干
- 面包板一个

### 对 ESP-01S 烧写程序的方法
| `esp-01s`引脚 | 对应`usb-ttl`引脚 |
|:-|:-|
| 3.3v | 3.3v |
| GND | GND |
| IO0 | GND |
| IO2 | NC |
| EN | 3.3v |
| RST | NC |
| TX | RX |
| RX | TX |

**esp-01s会在上电的瞬间检查IO0的电平，如果IO0为低点平则进入烧写模式，否则进入正常运行模式。**
**所以在烧写之前需要先断开esp-01s的Vcc链接，当Arduino-IDE出现开始上传的提示时插入Vcc。**
**烧写完成后，断开IO0与GND的连接，重新接通Vcc进入正常运行模式。**

### Arduino IDE 程序
```c++
#include <ESP8266WebServer.h>
#include <ESP8266HTTPClient.h>

const char* SSID = "********";
const char* PASSWORD = "********";

HTTPClient http;
ESP8266WebServer server(80);

const int relay = 0;

String getStatusJSON() {
  String json = "{ \"status\": 0, \"data\": { \"status\": ";
  json += digitalRead(relay);
  json += " } }";
  return json;
}

void connectWiFi(const char *ssid, const char *password) {
  WiFi.begin(ssid, password);
  Serial.printf("connecting to %s\n", SSID);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.printf("\nconnected to %s\n", ssid);
  Serial.print("ip address is: ");
  Serial.println(WiFi.localIP());
}

void setup(void){
  Serial.begin(115200);

  pinMode(relay, OUTPUT);
  digitalWrite(relay, 1);

  WiFi.mode(WIFI_STA);
  connectWiFi(SSID, PASSWORD);

  server.on("/on", []() {
    digitalWrite(relay, LOW);
    server.send(200, "text/json", getStatusJSON());
  });

  server.on("/off", []() {
    digitalWrite(relay, HIGH);
    server.send(200, "text/json", getStatusJSON());
  });

  server.on("/status", []() {
    server.send(200, "text/json", getStatusJSON());
  });

  server.onNotFound([]() {
    server.send(404, "text/plain", "Not Found");
  });

  server.begin();
  Serial.println("HTTP server started");
}

void loop(void){
  while (WiFi.status() == WL_CONNECT_FAILED || WiFi.status() == WL_CONNECTION_LOST || WiFi.status() == WL_DISCONNECTED) {
    Serial.printf("reconnect to %s\n", SSID);
    connectWiFi(SSID, PASSWORD);
  }
  server.handleClient();
}
```

### 我的完整解决方案
<iframe width="560" height="315" src="https://www.youtube.com/embed/vCTM-Y2L2Yg" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

### 视频中的源代码
