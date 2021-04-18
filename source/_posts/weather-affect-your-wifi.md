---
title: 雷雨天温度湿度会影响Wi-Fi
date: 2020-01-29 20:31:15
tags: wifi
---
## Weather Affect Your Wi-Fi

# 一. 背景

* 老家升级了光纤，但是没有光猫的超级管理员账号，无法查看和配置
* 使用过程中发现一个问题，恶劣天气是否会影响Wi-Fi
<!--more-->
* 结论：**<font style="color:black; background:yellow"> 有许多天气条件会严重削弱WiFi信号，但只会在室外条件下影响WiFi信号,降雨对WiFi信号的影响最大。你知道为什么吗？因为，无线电频率被雨滴吸收，并被雨滴部分阻挡，空气中的水分会打断路由器的信号的发送和接收。</font>**
* 
```shell
移动光猫账号： CMCCAdmin 密码：aDm8H%MdA
华为的：telecomadmin 密码：admintelecom
中国移动光纤宽带ip：192.168.100.1的超级账号：telecomadmin 密码：nE7jA%5m
中国移动默认光猫超级管理员账号密码汇总
已知：
型号HS8545M5 可用
```

# 二. 参考
* **由于频率越高，受到环境影响越大，电磁波在空气中传播的时候频率高的电磁波，更容易被反射和吸收**，下图是衰减系数表，随着频率增加，增益和距离的衰减系数会越来越大。因此**<font color="red">60GHz的频率在室外的时候，特别容易受到高湿度和下雨天影响的原因</font>**
* ![雨天的衰耗问题](http://www.irouteros.com/wp-content/uploads/2018/08/lhg60tu2.png)
* [**型号HS8545M5**](https://www.itren.org/171.html)
* [does-weather-affect-your-wifi](https://the-weather-station.com/does-weather-affect-your-wifi/)
* [can-humidity-affect-a-wi-fi-signal](https://www.techwalla.com/articles/can-humidity-affect-a-wi-fi-signal)
* [MikroTik 60GHz无线设备（上）](http://www.irouteros.com/?p=414)
