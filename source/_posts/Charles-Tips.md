---
title: Charles-Tips
date: 2021-01-01 16:58:56
tags: network
---

## 配置Charles 抓包手机和电脑流量

# 一. 背景
* 学习http和https的区别
  <!--more-->
* 钉钉app 找不到web端入口，且已购买的教程视频课程被禁止下载，通过抓包找到下载接口实现资源保存
# 二. 步骤
* 下载charles 4.6.1
* 点击`help =>SSL Proxying => Install Charles Root Certificate安装根证书 `
* 注意点击trust并选择始终信任
![image.png](https://i.loli.net/2021/01/20/CB6srH8SYb5upcF.png)
* 然后对在`SSL Proxying Settings里面配置对应的要抓取的https网站`
![image.png](https://i.loli.net/2021/01/20/hXfjpol7Sg5VLxY.png)
# 三. 参考
* [https://www.jianshu.com/p/14fdd1a2454a](https://www.jianshu.com/p/14fdd1a2454a)
* [软件测试教程Charles抓包工具测试实战](https://www.bilibili.com/video/BV1mQ4y1N7dn?p=1)

