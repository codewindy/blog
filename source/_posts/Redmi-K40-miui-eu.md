---
title: Redmi K40刷欧版miui
date: 2021-04-18 10:58:56
tags: life
---

## k40刷机教程

# 一. 背景
* 新买了2个k40手机，一个寄回老家，一个自用顺便把三年前的iphone 8plus替换下来
  <!--more-->
* 因为大陆版本广告太多了重置后浪费好多时间去设置关闭广告以及浏览器默认广告没法去除等等因素就决定刷机
* 安卓手机比iPhone优势在信号强，电池待机久，但是屏幕和通知权限等做的不好
> 注意下载miui stable稳定版，weekly update的版本无法自动更新系统，重新刷机很浪费时间
# 二. 步骤
* 准备环境先解锁unlock需要等待7天并下载好rom
* 然后连接到电脑fastboot模式下刷机等待即可
* 刷完机后需要安装magisk获取root以及本地化
* 实际刷了三次，前两次因为好多年没碰过安卓导致刷错了rom弄成转头了不过还在下载大陆rom刷回来了就是折腾浪费时间精力
* 按照以下参考链接教程[Redmi K40/POCO F3刷MIUI EU(欧洲版) 12.6+Root+大陆本土化套件选装](https://www.yongchangguo.com/archives/RedMiFlushToEUAndRoot.html)
* 但是我这边装完magisk后本地化`mi pay`和`应用商店`不兼容导致一直卡顿重启
* 效果图如下
* ![2021-05-22com.jpg](https://i.loli.net/2021/05/22/8vbo69lWzID4Urc.jpg)

# 三. 参考
* [apkpure](https://m.apkpure.com/) 下载纯净apk
* [Redmi K40/POCO F3刷MIUI EU(欧洲版) 12.6+Root+大陆本土化套件选装](https://www.yongchangguo.com/archives/RedMiFlushToEUAndRoot.html)
* [解锁工具](https://www.miui.com/unlock/index.html) 注意解锁需要绑定mi账号并且要等7 天
* [下载k40 rom](https://xiaomi.eu/community/threads/miui-12-0-12-1-12-2-12-5-stable-release.56191/)
* [安装magisk后修改boot.img得到magisk_patched-22100_DZXGd.img后](https://github.com/topjohnwu/Magisk/releases) 

  执行`platform-tools-windows\fastboot.exe flash boot ..\images\root\magisk_patched-22100_DZXGd.img`
* [大陆本土化套件熟悉](https://blog.minamigo.moe/archives/184)
* [大陆本土化套件安装，通过magisk的模块进行本地化根据提示安装下载导入到手机的0.72.zip](https://minamigo-my.sharepoint.com/:f:/g/personal/koizumishouta_minamigo_onmicrosoft_com/EgLhpcA_G_5IugTq10F90r0BzUbaO7aHamxpZCUUgBgefg?e=VVvXXd)