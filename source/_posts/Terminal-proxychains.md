---
title: proxychains代理
date: 2022-09-11 11:29:26
tags: proxychains
---

## 终端代理配置

# 一. 背景

* 目前可以通过All-Platform-Proxy.md 实现代理功能,但是提交代码的终端无法使用到代理
<!--more-->

# 二. 解决
* 下载[proxychains_0.6.8_win32_x64_debug.zip](https://github.com/shunf4/proxychains-windows/releases)
* 解压后并重命名文件`proxychains.exe`
* 在path 添加配置`C:\Program Files\proxychains_0.6.8_win32_x64_debug`
* 在c盘目录下创建`C:\Users\Administrator\.proxychains\proxychains.conf`并将配置文件最后面修改
    ```conf
    [ProxyList]
    socks5 localhost 10808 
    ```
* 效果图如下，在原来的命令前加`proxychains` 即可
* ![2022-09-11_110148proxychains](https://user-images.githubusercontent.com/15072465/189510527-a7931025-8b88-4536-ae0b-f3ce2afe1bf3.png)
