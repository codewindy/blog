---
title: RouterOS 常用配置优化和安全加固
date: 2020-04-18 11:32:48
tags: life
---

## RouterOS-Optimized

# 一. 背景

*  软路由系统ros安装完后默认会开启很多服务如ssh web ftp等，同时也带来了被攻击的风险，我们需要优化相关的配置
<!--more-->

# 二. 解决
```shell
# 将默认用户名admin更改为其他名称
/user set 0 name=myros
# 设置高强度的密码(红色字体为密码请自行更改)
/user set 0 password=“d*2bBsweUBe3@”
# 设置admin用户允许通过某个IP地址进行登陆(这里允许设置单个IP或多个IP也可以是IP段)
/user set 0 allowed-address=192.168.88.0/24
# 只保留安全的服务
/ip service disable telnet,f​​tp,www,api,api-ssl
⚠️注意：该操作会禁用Telnet，FTP，WWW，API，API-SSL
# 更改默认端口，这将立即停止大多数随机SSH暴力登录尝试
/ip service set ssh port=220
# 设置Winbox允许登陆的网段
/ip service set winbox address=192.168.88.0/24
# 禁用mac-telnet服务
/tool mac-server set allowed-interface-list=none
# 禁用mac-winbox服务
/tool mac-server mac-winbox set allowed-interface-list=none
# 禁用mac-ping服务
/tool mac-server ping set enabled=no
# 邻居发现
# MikroTik邻居发现协议用于显示和识别网络中的其他MikroTik设备，禁用所有接口上的邻居发现
# 禁用IPv4 的邻居发现协议
/ip neighbor discovery-settings set discover-interface-list=none
# 禁用IPv6 的邻居发现协议
/ipv6 nd set [find] disabled=yes
# 带宽服务器用于测试两个MikroTik路由器之间的吞吐量，请在测试后禁用它。
/tool bandwidth-server set enabled=no
# DNS缓存
/ip dns set allow-remote-requests=no
# 设置更安全的SSH访问，打开SSH强加密
/ip ssh set strong-crypto=yes
# 关闭 Proxy，Socks代理
/ip proxy set enabled=no
/ip socks set enabled=no
# MikroTik UPnP服务（通用即插即用协议）
/ip upnp set enabled=no
# MikroTik自带的DDNS服务器（动态域名解析）
# 如果不是使用的话请用以下命令禁用
/ip cloud set ddns-enabled=no update-time=no
# 某些型号的RouterBOARD有LCD模块用于信息显示。
/lcd set enabled=no
# 如果你的路由器不提供VPN服务，请用以下命令关闭VPN
/interface l2tp-server server set enabled=no
/interface pptp-server server set enabled=no
/interface sstp-server server set enabled=no
/interface ovpn-server server set enabled=no
# 禁用在设备上使用Radius进行授权
/user aaa set use-radius=no
# ⚠️⚠️⚠️⚠️移除操作请慎用
/radius remove numbers=[/radius find]
# 清空log
/system logging action set memory memory-lines=1
/system logging action set memory memory-lines=1000



/interface/wireless/export         
# jul/08/2020 22:19:41 by RouterOS 7.0beta8
# software id = xxx
#
# model = RBD52G-5HacD2HnD
# serial number = xxx
# 设置ntp服务器同步
/system ntp client
set enabled=yes
/system ntp client servers
add address=139.199.215.251 
# 203.107.6.88 alibaba

# 开启dhcp 服务器
/ip dhcp-server
add address-pool=dhcp disabled=no interface=bridge1 lease-time=1h name=dhcp1
/ip dhcp-server network
add address=192.168.2.0/24 gateway=192.168.2.2 netmask=24


# 优化hap ac2无线参数
# C- is center of frequency e - is extension channel   example : frequency is 5100 and in eCee will be see (5080-e,5100-C,5120-e,5140-e)
# fragmentation-threshold命令用来配置指定射频模板中的报文分段门限参数。缺省情况下，报文分段门限参数为2346Byte。应用场景配置合理的报文分段门限参数可以提高信道带宽的利用率。报文分段门限的设置需要用户根据实际情况进行选择，根据目前的发展趋势，建议用户采用较大值的门限。当报文分段门限设置过小时，报文就被分为多段传输，而在无线传输中，每传送一次都有较大的额外开销，因此信道利用率低,当报文分段门限设置过大时，长报文就不容易被分段，导致传输的时间长，出错的概率大，而一旦出错就要重传，因此会造成信道带宽的浪费。
# ts-cts模式：当AP向某个客户端发送数据的时候，AP会向客户端发送一个RTS报文，这样在AP覆盖范围内的所有设备在收到RTS后都会在指定的时间内不发送数据。目的客户端收到RTS后，发送一个CTS报文，这样在该客户端覆盖范围内所有的设备都会在指定的时间内不发送数据。使用rts-cts方式实现冲突避免需要发送两个报文，报文开销较大。
/interface wireless
set [ find default-name=wlan1 ] country=china mode=ap-bridge ssid=tpy wireless-protocol=802.11
/interface wireless security-profiles
set [ find default=yes ] authentication-types=wpa2-psk eap-methods="" group-key-update=1h mode=dynamic-keys supplicant-identity=MikroTik \
    wpa-pre-shared-key=a1234567 wpa2-pre-shared-key=a1234567
add authentication-types=wpa2-psk disable-pmkid=yes eap-methods="" group-key-update=1h mode=dynamic-keys name=eda supplicant-identity="" \
    wpa-pre-shared-key=a1234567 wpa2-pre-shared-key=a1234567
/interface wireless
set [ find default-name=wlan2 ] adaptive-noise-immunity=ap-and-client-mode band=5ghz-onlyac channel-width=20/40/80mhz-eeCe country=malaysia disabled=no \
    distance=indoors frequency=5300 hw-fragmentation-threshold=2346 hw-protection-mode=rts-cts installation=indoor keepalive-frames=disabled mode=\
    ap-bridge multicast-buffering=disabled multicast-helper=full security-profile=eda ssid=eda wireless-protocol=802.11 wps-mode=disabled
/interface wireless nstreme
set wlan2 enable-polling=no

# masquerade NAT 地址转换
/ip firewall nat
add action=masquerade chain=srcnat

# 支持Wi-Fi隐藏
[admin@jd] > interface/wireless/export 
# sep/27/2020 22:17:52 by RouterOS 7.1beta2
# software id = U01C-6QKJ
#
# model = RBD52G-5HacD2HnD
# serial number = CB390C02DA3C
/interface wireless
set [ find default-name=wlan1 ] country=china mode=ap-bridge ssid=tpy \
    wireless-protocol=802.11
/interface wireless security-profiles
set [ find default=yes ] authentication-types=wpa2-psk eap-methods="" \
    group-key-update=1h mode=dynamic-keys supplicant-identity=MikroTik \
    wpa-pre-shared-key=a1234567 wpa2-pre-shared-key=a1234567
add authentication-types=wpa2-psk disable-pmkid=yes eap-methods="" \
    group-key-update=1h mode=dynamic-keys name=eda supplicant-identity="" \
    wpa-pre-shared-key=a1234567 wpa2-pre-shared-key=wifipwd
/interface wireless
set [ find default-name=wlan2 ] adaptive-noise-immunity=ap-and-client-mode \
    band=5ghz-onlyac channel-width=20/40/80mhz-eeCe country=malaysia \
    disabled=no distance=indoors frequency=5300 hide-ssid=yes \
    hw-fragmentation-threshold=2346 hw-protection-mode=rts-cts installation=\
    indoor keepalive-frames=disabled mac-address=44:F9:71:8F:74:D9 mode=\
    ap-bridge multicast-buffering=disabled multicast-helper=full \
    security-profile=eda ssid=emoji wireless-protocol=802.11 wps-mode=disabled
/interface wireless nstreme
set wlan2 enable-polling=no
[admin@jd] > 


# kid control和hotspot 透传三层设备
  将斐讯等totalstub终端设备当成上面的kid control 和hotspot设备来使用，配置相应的mac地址和规则，实现有效限制上网的功能
```

![411594219877_.pic_hd.jpg](https://i.loli.net/2020/07/08/tT6yiZQ5mSugUbG.png)
![5g_wifi.png](https://i.loli.net/2020/07/11/bhL7oeHrIAQSawZ.png)

# 三. 参考
* [rosm.cn](https://rosm.cn/?p=670)
* [路由表每日更新](http://www.tcp5.com/)
* [RouterOS x86 1037u軟路由](https://kai3c.com/archives/291)
* [**RouterOS脚本编写与WLAN无线教程v66e** PDF电子版](http://www.irouteros.com/wp-content/uploads/2019/10/RouterOS%E8%84%9A%E6%9C%AC%E7%BC%96%E5%86%99%E4%B8%8EWLAN%E6%97%A0%E7%BA%BF%E6%95%99%E7%A8%8Bv66e.pdf)
* [ROS 在线脚本生成器](http://tools.rosjb.com/)
* [高厚网络](https://www.gaohou.com/soft.html)
* [Koolshare hap ac2](https://koolshare.cn/thread-166316-1-1.html)
* [AC6000](https://support.huawei.com/enterprise/zh/doc/EDOC1100064377/5fcf8329) ts-cts模式  fragmentation-threshold命令
* [hAP ac²](http://www.irouteros.com/?p=282) https://www.ixbt.com/nw/mikrotik-hap-ac2-review.html
* [routore-mikrotik-hap-ac-2](https://netflow.by/blog/inet-setup/1986-nastrojka-wi-fi-na-routore-mikrotik-hap-ac-2)
* [Since routeros-all-2.9.51](http://www.mikrotik.com.ua/download/routeros/)
* [RouterOS WiFi隐藏SSID和隔离WiFi客户端配置](http://www.irouteros.com/?p=637)
* [RouterOS 802.11ac双频合一配置](http://www.irouteros.com/?p=356)
* [MikroTik 60GHz无线设备（上）](http://www.irouteros.com/?p=414) 由于频率越高，受到环境影响越大，电磁波在空气中传播的时候频率高的电磁波，更容易被反射和吸收，下图是衰减系数表，随着频率增加，增益和距离的衰减系数会越来越大。因此60GHz的频率在室外的时候，特别容易受到高湿度和下雨天影响的原因
* [ros6.com](http://ros6.com/)
* [chr安装](http://www.clxp.net.cn/thread-17315-1-1.html)
* [ROS6.43_x64.ova](https://www.myxzy.com/post-476.html)
* [DDoS Protection ](https://help.mikrotik.com/docs/display/ROS/DDoS+Protection)
* [mikrotik-unicode-ssid-generator](https://r-1.ch/mikrotik-unicode-ssid-generator.php) 支持`emoji`

