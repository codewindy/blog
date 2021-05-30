---
title: Proxifier 全局代理
date: 2019-10-14 22:07:02
tags: mac
---

Proxifier MacOS Setting
---
# 一.  背景

* 装完macos系统后，一直不能像win10一样轻松上Twitter和Google，试了n种软件终于找到一种可用的方式
  <!--more-->

# 二.  准备条件
* **license key for mac **  `P427L-9Y552-5433E-8DSR3-58Z68` 
* 下载   [Proxifier v2.24](https://proxifier.com/download/ProxifierMac.dmg) 
* 下载  配置文件 [proxifierRules4mac.ppx](https://github.com/codewindy/mac_soft/blob/master/proxifierRules4mac.ppx)
* 下载  [shadowsocksX-NG-R8](https://github.com/codewindy/mac_soft/blob/master/ShadowsocksX-NG-R8.dmg)

# 三.  安装
* 先安装 `shadowsocksX-NG-R8`
* 然后安装 `Proxifier` 并使用license注册
* 然后导入配置文件`proxifierRules4mac.ppx`

# 四.  效果
* 已经能正常使用Google和Twitterrific了
* ![Proxifier-Tutorial.jpg](https://i.loli.net/2019/10/14/EUSiaK9mWbBgNJH.jpg)
# 五.  其他方式
* 直接在`路由器geewan`[固件GF_PSG1218_K2 - bigwan 0.0.23-fix10 ](http://dl.geewan.com/%E5%85%A8%E9%83%A8%E5%8E%86%E5%8F%B2%E5%9B%BA%E4%BB%B6/0.0.23/)或者`极路由等LEDE `刷机里面配置好`ssr `代理即可全局上网，但是使用过程中发现灵活性较差
* 直接使用chrome免费插件 [**skyzip**](https://github.com/codewindy/soft-common/blob/master/skyzip.crx)


# 六.  免费节点

* [几鸡-学习助理](https://ji-f.pw/signin) 台湾的学习免费节点网速很棒，注册需要能先上Google
* [lncn.org](https://lncn.org/) 能满足日常使用，但需要配置hosts文件
* [xueyun.club](https://xueyun.club/auth/register)
* [freeyes.xyz](https://freeyes.xyz)
  
```shell
  在本地host文件中将本站域名绑定到 69.194.14.16
  安卓可下载 Hosts Go 添加 ， windows具体操作方法如下↓
  目录：C:/Windows/System32/drivers/etc，记事本打开host文件
  在最后一行添加(注意空格)： 69.194.14.16 lncn.org
  win+R 输入cmd回车，执行： ipconfig /flushdns
  重新刷新页面即可,如失效请还原host并用代理访问！
  macOS/Linux 下直接 echo "69.194.14.16 lncn.org" >> /etc/hosts 
  export http_proxy=http://127.0.0.1:1087 经常会github连不上 可以curl cip.cc查看ip地址
```
# 七.  参考
* [kagura.me](https://www.kagura.me/dev/20190324005534.html)   神楽
* [lncn.org](https://lncn.org/)   免费节点
* [几鸡-学习助理](https://ji-f.pw/signin) 台湾学习助理
* [geewan](http://dl.geewan.com/%E6%96%90%E8%AE%AF%E8%B7%AF%E7%94%B1-Phicomm/%E6%9C%80%E6%96%B0%E5%85%8D%E6%8B%86%E6%9C%BA%E6%96%90%E8%AE%AFK1K2%E5%88%B7%E6%9E%81%E7%8E%A9%E5%9B%BA%E4%BB%B6%E6%95%99%E7%A8%8B.rar) 斐讯k2/极路由固件教程
* [ClashX Pro](https://install.appcenter.ms/users/clashx/apps/clashx-pro/distribution_groups/public)
* [v2ray-clients-download](https://tlanyan.pp.ua/v2ray-clients-download/)
* [雷霆加速器](https://www.rufrsp.com/) www.leiting66.com
* [黑洞加速器](https://www.heidongfast.com)
* [Clash for Windows/Android apk](https://docs.cfw.lbyczf.com) Clash 各个平台软件下载
* [金坷垃](https://jinkela001.xyz/auth/register/)  免费小飞机场