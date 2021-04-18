---
title: 打不开网页,重置ie浏览器
date: 2018-12-15 22:15:48
tags: life
---

## 重置ie浏览器

# 一. 背景

*  电脑设置代理关掉后会经常出现不能打开网页的情况
<!--more-->

# 二. 解决
* 在win10下新建一个bat脚本文件并执行
```shell
  @echo off
  rundll32 inetcpl.cpl ResetIEtoDefaults
  set p=rundll32 inetcpl.cpl ResetIEtoDefaults
  set q=reset successful
  echo %p%
  echo %q%
  )
  pause
```