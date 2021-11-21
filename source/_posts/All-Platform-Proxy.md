---
title:  全平台高速上网的方法
date: 2021-08-15 16:58:56
tags: life
---

##  全平台上网方式

# 背景
* 由于自己有全平台设备导致各个设备上网尤其是`Youtube `和`Twitter`就很繁琐，现在发现了一种全新的全局上网可以覆盖全平台，而且成本很低，最重要的是速度又快又稳定
* **整个过程 Redmi k40 安卓手机充当者路由器的角色, 连接WiFi或使用4G流量，然后发射热点提供正向代理以及流量转发**

* 我的设备如下表

  | 设备名                      | 系统          | 软件                 |
  | --------------------------- | ------------- | -------------------- |
  | Redmi k40     买偶数196g     | Android       | [pandaVPN](https://github.com/codewindy/Mikrotik-Phicomm-Backup)  everyproxy |
  | iPhone 8 plus          202g | IOS           | [shadowrocket](https://1.v2w.org/ios/)         |
  | hasee z7-kp7gz              | window 10     | [proxifier 下载](https://www.proxifier.com/download/)            |
  | MacBook pro 2019 【黑苹果】   | macOS Big Sur | [proxifier 下载](https://macwk.com/soft/proxifier)           |
* 纪念一下今天早上出门过天桥摔倒了左手臂着地以及电脑包也是，到自习的地方发现左手臂一摸就很痛以及电脑转轴也坏了不影响使用但是淘宝问了一下修一次又要好几百内心很奔溃，才用了两年的新电脑摔了不完美了内心不舒服
* 现在是2021-09-12 左手还没好还有点痛啊，以及这段时间发生了很多变化，需要自己妥善处理

#  步骤
* 下载  [**熊猫vpn**](https://github.com/codewindy/Mikrotik-Phicomm-Backup) 仅限 Android ，记住 隔 4h清除app 的应用数据可以无限制重新连接
*  或者下载[**letsvpn-3.1.1.exe**](https://letsvpn.world/) 快连VPN 秒开YouTube 速度快  然后执行破解1小时无限试用[unlimited patch](https://github.com/codewindy/win-toolbak/blob/master/%E5%BF%AB%E8%BF%9EVPN%E4%B8%80%E9%94%AE%E7%A0%B4%E8%A7%A3%E8%A1%A5%E4%B8%813.0.exe)
* ![Screenshot_2021-08-15-14-19-05-058_com.pandavpn.androidproxy.jpg](https://i.loli.net/2021/08/15/sBKebS27r8Zojd9.jpg)
* 然后安装  [Every Proxy](https://apkpure.com/every-proxy/com.gorillasoftware.everyproxy/download?from=details) 并简单设置一下 `http 代理`或者`socket5 代理`
* ![image.png](https://i.loli.net/2021/08/15/7ROWiqKFtPoaDJn.png)
* 在安卓手机Redmi k40 上开启无线热点
* 最后在电脑上使用[ **proxifier** ](https://www.proxifier.com/download/)配置一下无线热点对应的ip 地址`192.168.167.5 `以及 every proxy 上配置的自定义代理端口号`1088 `就能使用了
* 其实一开始是想着抓包或者直接Root Explore 后获取`/data/usr/0/com.pandavpn.androidproxy` 目录下找到了对应的服务器地址以及连接账号密码但是用ssr试了发现无法使用应该是被限制了而且密码是每次连接随机生成的还加上了超时机制等于是作罢
* ![panda_VPN_2021-08-15.jpg](https://i.loli.net/2021/08/16/QOqDbYjIrzEZfsV.png)
* 在ios 以及macos 端也可以照着这种方式实现高速刷Twitter
* **ios端shadowrocket 在连接热点的时候手动设置http代理有的软件可能还是无法使用，最好设置socket5代理，即 点击 + 号在类型里面填socket5 并填入热点的ip 地址`192.168.167.5 `以及 every proxy 上配置的自定义代理端口号`1088` 就能使用了**
* ![Screenshot_2021-10-09-14-30-41-321_com.miui.home.jpg](https://i.loli.net/2021/10/09/rPvyYt2TSaDg8Ji.jpg)
* ~~截至时间`2021-10-31` 老版本可以用 **PandaVPN_v1.6.2会员版.apk** ，目前`pandaVPN`已经被禁了免费试用HK节点，可以换连接稍慢的节点乌克兰-哈尔科夫,可以下载1.6版本或者使用`ExpressVPN`来替代, 连接时间和速度稍微差一点可能是换回miui国内版的原因吧。~~
* 截至时间`2021-11-07` 可以使用[**优途加速器_v1.1.29.apk**](https://www.mediafire.com/file/zm22yrvw2gwveb8/优途加速器_v1.1.29.apk/file) 秒连HK 速度还不错
# 参考
* [Every Proxy](https://www.everyproxy.co.uk/)  作用相当于 **本地代理允许来自局域网的连接**
* [**熊猫vpn**](https://github.com/codewindy/Mikrotik-Phicomm-Backup) 
* [**letsvpn 官网**](https://letsvpn.world/)  [mediafire下载地址](https://www.mediafire.com/file/9qkxeia7ur21z4d/letvpn3.0.zip/file)  https://download.dwladold.xyz/windows/letsvpn-3.0.0.exe 
* [Proxifier教程](https://codewindy.github.io/2019/10/14/Proxifier-Setting/)
* [ExpressVPN MOD APK 10.10.0 (Unlimited Trial)](https://apkdone.com/expressvpn/)
* [a2zapk.com](https://a2zapk.com/History/com.expressvpn.vpn/)
* [emo_VPN_1.0.0.apk](https://emo001.club/)
* [AnXray.apk](https://github.com/XTLS/AnXray/releases)
* [优途加速器_v1.1.29.apk](https://www.mediafire.com/file/zm22yrvw2gwveb8/优途加速器_v1.1.29.apk/file)
* [PandaVPN_v1.6.2会员版.apk](https://www.mediafire.com/file/uhvk81ptquxdxyj/PandaVPN_v1.6.2会员版.apk/file)


