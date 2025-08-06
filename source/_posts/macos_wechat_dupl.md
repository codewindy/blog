---
title: macos 微信双开
date: 2025-04-18 10:58:56
tags: life
---

## macos 微信双开

# 一、背景
* 需要双开微信办公

# 二、步骤
### 1. 创建分身

```shell
sudo cp -R /Applications/WeChat.app /Applications/WeChat2.app      
```
### 2. 给wechat2.app 授权

```shell
sudo /usr/libexec/PlistBuddy -c "Set :CFBundleIdentifier com.tencent.xinWeChat2" /Applications/WeChat2.app/Contents/Info.plist
```
### 3. 签名

```shell
 sudo codesign --force --deep --sign - /Applications/WeChat2.app         
```

### 4. 运行微信

```shell
 nohup /Applications/WeChat2.app/Contents/MacOS/WeChat > /dev/null 2>&1 &  
```

### 5. 把第二个微信放到 docker 栏即可

![](https://img.erpweb.eu.org/imgs/2025/08/18f791b24142ede0.png)

# 三、参考
* https://blog.csdn.net/D1237890/article/details/147544117