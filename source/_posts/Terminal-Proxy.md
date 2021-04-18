---
title: MacOS 下 Terminal 设置代理上网
date: 2019-11-21 21:34:41
tags: mac
---
MacOS Terminal Proxy Settings
---

# 一.  背景

* 装完macos系统后，经常使用`Terminal`终端来写blog提交到github，但是老是丢包，最终通过代理来实现
  <!--more-->

# 二.  准备
* [ShadowsocksX-NG](https://github.com/qinyuhang/ShadowsocksX-NG-R/releases)  软件并配置好可用的ssr节点

# 三. 实现
* 打开[https://www.diggui.com/](https://www.diggui.com/) 查询`github.com`的A 记录并添加到`sudo vim /etc/hosts`

  ```shell
  A:github.com@8.8.8.8
  ; <<>> DiG diggui.com <<>> @8.8.8.8 github.com A
  ; (1 server found)
  ;; global options: +cmd
  ;; Got answer:
  ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 46485
  ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
  
  ;; OPT PSEUDOSECTION:
  ; EDNS: version: 0, flags:; udp: 512
  ;; QUESTION SECTION:
  ;github.com.			IN	A
  
  ;; ANSWER SECTION:
  github.com.		59	IN	A	140.82.113.4
  
  ;; Query time: 9 msec
  ;; SERVER: 8.8.8.8#53(8.8.8.8)
  ;; WHEN: Tue Jan 14 14:24:33 UTC 2020
  ;; MSG SIZE  rcvd: 55
  ```

* 在`~/.zshrc `文件中配置
```shell
  alias setroxy='export http_proxy=http://127.0.0.1:1087;
  export https_proxy=http://127.0.0.1:1087;'
  alias unsetproxy='unset http_proxy https_proxy'
  alias ip='curl cip.cc'
```
* 只配置git代理
```shell
 #设置
 git config --global http.proxy 'socks5://127.0.0.1:1086' 
 git config --global https.proxy 'socks5://127.0.0.1:1086'
 #取消
 git config --global --unset http.proxy
 git config --global --unset https.proxy
 git clone https://github.com/codewindy/blog.git --config "http.proxy=127.0.0.1:1086"
 
 
 #只对github.com
git config --global http.https://github.com.proxy socks5://127.0.0.1:1086

#取消代理
git config --global --unset http.https://github.com.proxy

#只对github.com
git config --global http.https://github.com.proxy socks5://127.0.0.1:7891

#取消代理
git config --global --unset http.https://github.com.proxy
```
*  在斐讯k2路由器刷机geewan或者openwrt/LEDE等石像鬼系统，直接配置ssr节点在路由器里面实现全局代理
![Xnip2019-11-22_21-36-42.jpg](https://i.loli.net/2019/11/22/2SZVI5WJmxYLdOP.jpg)
# 四. 效果
 ![Xnip2019-11-21_22-02-55.jpg](https://i.loli.net/2019/11/21/NalKSW27cfmC5pR.jpg)

# 五. 参考
* [blog.naaln.com](https://blog.naaln.com/2019/03/terminal-proxy/)
* [gist config ](https://gist.github.com/laispace/666dd7b27e9116faece6)
* [https://www.diggui.com/](https://www.diggui.com/)
