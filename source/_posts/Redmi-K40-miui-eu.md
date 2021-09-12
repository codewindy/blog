---
title: Redmi K40刷欧版miui
date: 2021-04-18 10:58:56
tags: life
---

## k40刷机教程

# 背景
* 新买了2个k40手机，一个寄回老家，一个自用顺便把三年前的iphone 8plus替换下来
  <!--more-->
* 因为大陆版本广告太多了重置后浪费好多时间去设置关闭广告以及浏览器默认广告没法去除等等因素就决定刷机
* 安卓手机比iPhone优势在信号强，电池待机久，但是屏幕和通知权限等做的不好
> 注意下载miui stable稳定版，weekly update的版本无法自动更新系统，重新刷机很浪费时间

# 步骤
* 准备环境先解锁unlock需要等待7天并下载好rom
* 然后连接到电脑fastboot模式下刷机等待即可
* 刷完机后需要安装magisk获取root以及本地化
* 实际刷了三次，前两次因为好多年没碰过安卓导致刷错了rom弄成转头了不过还在下载大陆rom刷回来了就是折腾浪费时间精力
* 按照以下参考链接教程[Redmi K40/POCO F3刷MIUI EU(欧洲版) 12.6+Root+大陆本土化套件选装](https://www.yongchangguo.com/archives/RedMiFlushToEUAndRoot.html)
* 但是我这边装完magisk后本地化`mi pay`和`应用商店`不兼容导致一直卡顿重启
* 效果图如下
* ![2021-05-22com.jpg](https://i.loli.net/2021/05/22/8vbo69lWzID4Urc.jpg)
* 彻底试了刷miui.eu 需要自己手动刷twrp才能实现自动升级，否则每次都需要自己重新刷系统来实现更新，
  每次都会数据备份和恢复很繁琐，用大陆版本就可以省事但是开YouTube和VPN 可能会导致系统奔溃各有优劣吧

# 参考
* 注意刷欧版miui后不能手动升级系统需要下载对应的[twrp-350-root-xiaomi-redmi-k40](https://unofficialtwrp.com/twrp-350-root-xiaomi-redmi-k40/)
* [mifirm](https://mifirm.net/mostdownload) 下载ROM 以及TWRP
* [apkpure](https://m.apkpure.com/) 下载纯净apk
* [mail.yandex.com](https://lncn.org/) 注册yandex mail 和disk需要使用Russia的VPN节点云盘实现类似Google Photos的图片上传备份
* [www.yxssp.com](https://www.yxssp.com/23740.html) 破解版安卓软件下载 IDM+ 滴答清单 rosettaStone
* [Redmi K40/POCO F3刷MIUI EU(欧洲版) 12.6+Root+大陆本土化套件选装](https://www.yongchangguo.com/archives/RedMiFlushToEUAndRoot.html)
* [解锁工具](https://www.miui.com/unlock/index.html) 注意解锁需要绑定mi账号并且要等7 天
* [下载k40 rom](https://xiaomi.eu/community/forums/miui-12.206/)
* [twrp k40](https://androidfilehost.com/?fid=2188818919693798739)
* [xiaomi.shxj.pw](https://xiaomi.shxj.pw/android-phone/k40/1230/)
* [安装magisk后修改boot.img得到magisk_patched-22100_DZXGd.img后](https://github.com/topjohnwu/Magisk/releases) 

  执行`platform-tools-windows\fastboot.exe flash boot ..\images\root\magisk_patched-22100_DZXGd.img`
* [大陆本土化套件熟悉](https://blog.minamigo.moe/archives/184)
* [大陆本土化套件安装，通过magisk的模块进行本地化根据提示安装下载导入到手机的0.72.zip](https://minamigo-my.sharepoint.com/:f:/g/personal/koizumishouta_minamigo_onmicrosoft_com/EgLhpcA_G_5IugTq10F90r0BzUbaO7aHamxpZCUUgBgefg?e=VVvXXd)