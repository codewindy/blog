
---
title: Debug-Eclipse
date: 2015/8/5 17:19
tags: debug


---

##Debug 调试快捷键
<font color=red>好记性不如烂笔头,debug不论在学习还是工作中都能很好的分析和解决问题</font>
<!-- more -->

* F5
    `单步调试，遇到方法时跳入`
* F6
    `单步调试，遇到方法时跳过`
* F7
    `单步调试，遇到方法时跳出`
* F8
    `直接执行，遇到断点时暂停`
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-5/98352807.jpg)


##视图
* 变量视图
    `监视/修改变量的值`
 ![](http://ot2hu9qoc.bkt.clouddn.com/17-8-5/20113069.jpg)



* 断点视图
    `断点列表`
  ![](http://ot2hu9qoc.bkt.clouddn.com/17-8-5/28292010.jpg)

* 表达式视图
    `计算表达式的值`
    `快捷键：Ctrl + Shift + I ，选中代码编辑区中的表达式按快捷键`

  ![](http://ot2hu9qoc.bkt.clouddn.com/17-8-5/27737463.jpg)


##条件断点

断点满足条件时触发

* 设置
    `选中需要条件触发的断点 >> 右键 >> Breakpoint Properties 打开属性断点属性窗口`

    `勾选 "Hit count"，输入循环次数`
    `勾选 "Conditional"，输入条件表达式`

  ![](http://ot2hu9qoc.bkt.clouddn.com/17-8-5/44775967.jpg)

##springmvc的小误区

* 请求参数的方法
 ![](http://ot2hu9qoc.bkt.clouddn.com/17-8-5/85811164.jpg)
* 错误和正确的写法
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-5/49918100.jpg)

* 根源是: eclipse的debug模式会自动给java类反射对象注入参数

![](http://ot2hu9qoc.bkt.clouddn.com/17-8-5/34979265.jpg)

##maven debug 源码关联添加
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-7/36374310.jpg)

* Go to Help > Install New Software .. 安装插件
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-7/36051185.jpg)
* http://ifedorenko.github.com/m2e-extras/
* to search for the links. After installing this plugin I was finally able to debug the source code of dependencies.


* 最后实现查看
* springmvc的的DispatcherServlet 的doDispatcher方法执行
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-7/38348793.jpg)
* 具体的业务逻辑代码 service 的执行

![](http://ot2hu9qoc.bkt.clouddn.com/17-8-7/47074681.jpg)