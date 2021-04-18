---
title: 斐讯k2通过编程器刷breed后升级padavan固件
date: 2019-12-15 21:07:45
tags: life
---
## 斐讯k2路由器通过ch340编程板刷入breed

# 一. 背景

*  2017年在`jd自营`买的斐讯k2路由器自动更新固件到`22.6.532.231`，网络经常会出现卡顿，于是动了刷机的心思
<!--more-->
# 二. 准备条件
* 先认真阅读这篇恩山论坛的教程  <font style="color:black; background:yellow">[**航校门口二楼修**](https://www.right.com.cn/FORUM/thread-636761-1-1.html)</font>
* 淘宝购买 ch340编程板并下载[相应的驱动](http://www.wch.cn/products/CH340.html )也可以找卖家要
* 下载[tftpd32](http://tftpd32.jounin.net/ ) 服务器
* 下载k2 对应的 [老毛子padavan 固件](https://github.com/hanwckf/rt-n56u/releases)/或[极玩固件](http://dl.geewan.com/)
* 下载[xshell免费版](https://github.com/codewindy/mac_soft/blob/master/Xshell-6.0.0181p_2.exe.7z)或secureCRT或putty等可以连接 串口软件

# 三. 操作
* 关闭win10防火墙，总共需要关闭三个地方
* 安装ch340驱动 `win+pause`打开win10属性后点击设备管理器里面会多一个串口，短接5v **注意接好线后只会亮一个红灯，我试了2个晚上，第一次错以为要三个灯全部亮，其实还是接线的问题浪费大量时间**
* 插入ch340，并打开xshell按serial串口方式接入
* 给k2路由器接电然后一直按9就行了，后面直接输入路由器的ip地址和breed.bin输入成功
* 断电然后按住reset按钮5s 后 等路由器重启之后xshell会打印出`breed>`成功进入breed，后面可以刷入任意固件了
# 四. 问题解决
* `按9 没反应一直使用默认的3 启动的`是因为淘宝5块钱买的ch340 杜邦线是母对母，需要自己额外买母对公或者自己把线割开用铜丝接入**是线跟编程器接触不良导致的rx线信号没有传入，需要重新插好线，电源短接5v **
* 刷完`padavan 老毛子固件`之后需要重置reset 到出厂设置，然后设置Wi-Fi 信号地区选择 **Australia**
# 五. 参考
* https://www.right.com.cn/FORUM/thread-636761-1-1.html
* http://www.wch.cn/products/CH340.html 编程器板驱动
* https://github.com/codewindy/mac_soft/blob/master/Xshell-6.0.0181p_2.exe.7z xhell 串口模式连接
* http://tftpd32.jounin.net/ tftp服务器
* https://github.com/hanwckf/rt-n56u/releases k2刷机固件
* http://dl.geewan.com/ 极玩固件
* https://github.com/codewindy/soft-common/blob/master/k2_breed_padavan.7z
* http://opt.cn2qq.com/padavan/
* https://www.right.com.cn/FORUM/thread-636761-1-1.html
* www.duomingxing.com/down/mtk68.php
* [k2p官改固件下载](https://www.mingjinglu.com/write/548.html)
* [K2官改固件github下载](https://github.com/mayunbaba2/K2)