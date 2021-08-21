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
  | Redmi k40                   | Android       | pandaVPN  everyproxy |
  | iPhone 8 plus               | IOS           | shadowrocket         |
  | hasee z7-kp7gz              | window 10     | proxifier            |
  | MacBook pro 2019 【黑苹果】   | macOS Big Sur | proxifier            |
* 纪念一下今天早上出门过天桥摔倒了左手臂着地以及电脑包也是，到自习的地方发现左手臂一摸就很痛以及电脑转轴也坏了不影响使用但是淘宝问了一下修一次又要好几百内心很奔溃，才用了两年的新电脑摔了不完美了内心不舒服

#  步骤
* 下载  [熊猫vpn](https://github.com/codewindy/Mikrotik-Phicomm-Backup) 仅限 Android ，记住 隔 4h清除app 的应用数据可以无限制重新连接
* ![Screenshot_2021-08-15-14-19-05-058_com.pandavpn.androidproxy.jpg](https://i.loli.net/2021/08/15/sBKebS27r8Zojd9.jpg)
* 然后安装  [Every Proxy](https://apkpure.com/every-proxy/com.gorillasoftware.everyproxy/download?from=details) 并简单设置一下 `http 代理`或者`socket5 代理`
* ![image.png](https://i.loli.net/2021/08/15/7ROWiqKFtPoaDJn.png)
* 在安卓手机Redmi k40 上开启无线热点
* 最后在电脑上使用[ proxifier ](https://www.proxifier.com/download/)配置一下无线热点对应的ip 地址`192.168.167.5 `以及 every proxy 上配置的自定义代理端口号1088 就能使用了
* 其实一开始是想着抓包或者直接Root Explore 后获取`/data/usr/0/com.pandavpn.androidproxy` 目录下找到了对应的服务器地址以及连接账号密码但是用ssr试了发现无法使用应该是被限制了而且密码是每次连接随机生成的还加上了超时机制等于是作罢
* ![panda_VPN_2021-08-15.jpg](https://i.loli.net/2021/08/16/QOqDbYjIrzEZfsV.png)
* 在ios 以及macos 端也可以照着这种方式实现高速刷Twitter
* ios端shadowrocket 在连接热点的时候手动设置http代理有的软件可能还是无法使用，最好设置socket5代理，即 点击 + 号在类型里面填socket5 并填入热点的ip 地址`192.168.167.5 `以及 every proxy 上配置的自定义代理端口号1088 就能使用了
* ![image.png](https://i.loli.net/2021/08/16/ojxNsJm8Inf2D4r.png)

# 参考
* [Every Proxy](https://www.everyproxy.co.uk/)  作用相当于 **本地代理允许来自局域网的连接**
* [熊猫vpn](https://github.com/codewindy/Mikrotik-Phicomm-Backup) 
* [Proxifier教程](https://codewindy.github.io/2019/10/14/Proxifier-Setting/)

