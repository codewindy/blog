---
title: bug汇总
date: 2020-07-04 13:42:16
tags: java
---
 Squashes  Bug
---
# 一. 需要注意⚠️的问题
* 因为八进制导致的问题，开发中尽量使用String来定义code变量
 <!--more-->
*  数据库字段和代码编写的过程中多注意 <font color="red">数据可能包含多余的空格</font>导致的问题
*  在前端传时间参数在后台需要进行处理TimeZone时区的问题

```java
      Integer code =001001;
      // 结果确是 被转换成8进制code = 513
      System.out.println("code = " + code);
      // 引入hutool依赖
      DateUtil.beginOfDay(startDate);
      DateUtil.endOfDay(endDate);
```
*  <font color="blue">maven依赖项目common模块</font> 没有deploy到仓库, 导致打包的应用有代码没更新出错
*  [**idea out build目录区别以及启动后代码和配置更新问题** ](https://m.jb51.net/article/196376.htm) 
*  开发规范 spotbugs p3c插件
*  [angular.js 开发提交规范 ](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md)`git commit guidelines`
*  需求串讲责任人开发
*  1990年时间查询差1小时问题GMT+8
*  harbor rancher chart配置
*  sql 隐式转换int str不走索引
*  requestParam 不能用post请求
*  [一次web请求触发了哪些操作](https://nyimac.gitee.io/2020/12/10/URL%E8%AE%BF%E9%97%AE%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B5%81%E7%A8%8B/)
