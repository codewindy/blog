---
title: Java 面试题
date: 2021-06-13 23:29:26
tags: Java
---

## Java 面试题

# 背景

* 准备换工作了，总结复习一下迎接下一个挑战
<!--more-->

# 面经
```
蚂蚁金服 一面

一：设计模式用过哪些，有高级的吗
二：mock框架如何实现的，你们开发mock用来干嘛的
三：平时如何排查线上问题的
四：你说你们项目中线上遇到full gc，你如何知道full gc 出现在哪台机器上，如何排查？ 我：优化调大了 年轻代和老年代的比例，让大对象不直接进入老年代。
那这么调实际上内存还是没什么变化，能有用吗？ 我：其实一开始出现问题，先从业务层面优化，业务层面无法优化，在考虑机器
五：你们业务系统有那么多服务，为什么要用dubbo，用http不是也可以吗？ http协议和dubbo协议有什么区别？
六：了解线程池吗
七：上线之前的风险，你如何识别，如何排查，你们有没有做代码监控，和线上出现的问题监控
 Append ： 清算系统架构与设计 对公对私收单并封装统一接口出来  
           ElasticSearch 移除type的原因

蚂蚁金服 二面
/**
* /**
* * 输入1个由数字组成的字符串和1个整数，输出字符串中连续数字和等于所给整数的子字符串
* * 若找不到符合要求的子字符串，则返回空
* * 若找到多于一个符合要求的子字符串，则返回长度最短的一个
* *
* * 举例：
* * "954891", 17 => "89"
* *
* * 要求：
* * - 可运行并返回正确结果，保证健壮性
* * - 设计测试用例，验证实现方法正确性
* * - 加分 时间复杂度为2n
* */
Append：  ElasticSearch 集群
          Redis 场景问题 以及 与数据库同步一致问题
         你遇到哪些线上的bug 如何修复的？
          mq 保证不丢失如何做的
         你作为团队里面的哪个角色？

蚂蚁金服 三面
一：你在公司主要担任什么角色，干了哪些
二：主要负责的资金方面的功能，出现了bug，出现了资金损失，你怎么去排查风险，有没有监控
三：如果公司出现了技术bug ，导致了财务对账对不上，你后期如何解决，有没有专门识别的工具。
四：设计模式在项目中如何使用的
五：乐观锁和悲观锁你们在项目中用了吗，用在什么场景，如何使用，为什么要这么做
六：java中的锁有哪些
七：为什么要做mock框架，跟sring如何整合的

蚂蚁金服 四面（hrg）
一：在公司担任什么工作
二：你的优点和缺点
三：对未来的职业生涯规划
四：在工作中最有成就感的事情
五：你们的绩效kpi是如何做的怎么量化的
六：你们技术团队架构是怎样的
七：你们小组的技术开发人员有多少？全都是开发吗？
八：你觉得这个工作和你原来的工作有什么区别
九：你了解我们是做什么业务的吗
十：有什么有什么要问我的


滴滴出行 一面

一：广度搜索和深度搜索的区别
二：数据模型的难点在哪，如何设计支持多级
三：threadlocal原理，里面的map有什么特性
四：常见的垃圾回收器 和 每种垃圾回收器用的哪些算法
五：spring bean的生命周期
六：循环依赖如何解决
七：srping的事务，注解加在private方法上有用吗？没用 ，为什么没用，动态代理的实现方shi, jdk如何生成动态代理对象的
八：innodb的事务
九：公平锁和非公平锁 jdk如何实现的

shopee面试 20210805 

1. 问了进程线程 操作系统直接cpu分片给线程
2. redis io 单线程效率高 ttl  redis 一致性hash环以及集群分片等
3. aufs 减小docker文件大小
4. 反转单向链表    用linklist 实现后发现时间复杂度O(n) 被面试官嫌弃 ，后面百度查到了更好简单的方法 但是我没准备leecode 这块直接gg 
5. 取倒数第k 大值  回答 冒泡/快排 后取数组下标
6. http 协议 quic http2
7. 乐观锁 悲观锁   synchronized  /lock
8. 如何查询第二次非聚簇索引
9. docker 里面添加后面删除镜像大小是多大
10. heap 的数据结构 不是讲jvm 好像他不关心也不问这个

/**
 * 反转单链表
 *
 * 详细描述
 *
 * 3->5->6->2
 *
 * 反转之后
 *
 * 2->6->5->3
 */

顺丰科技
 
1. Spring 循环依赖
2. Redis 集群分片查找 一致性hash环
3. MySQL 联合索引 索引覆盖 a_b_c 索引生效的情况
4. term match 查询的区别

涂鸦智能 20210810
1. mq 发送broker失败后会丢失吗？
2. CMS 与 G1 垃圾回收器的区别
3. ReentrantLock / 公平锁与非公平锁状态下aqs 里面的state 区别
4. synchronized 在JDK 1.6 之后做了哪些优化
5. 读写锁 你熟悉吗？
6. GC ROOT 可达性分析
7. 标记清除算法与 整理算法区别
8. redis SDS 动态扩容
9. aof 与rdb 的区别

保利集团保利物业

1. Eureka 与Zookeeper 区别 答复 CAP理论
2. Eureka 自我保护机制 一直追问 eureka
3. update xx 表的过程 数据页读取到 buffer 等过程 x
4. Redis 过期机制的实现 LRU
5. 分布式事务的使用 
6. volatile 实现原子性与可见性
7. ThreadLocal 原理
8. hashmap 扩容机制
9. CAS原理
10. synchronized / reentrantlock 区别
11. 自旋锁 拿不到值 是否会一直等待
12. 什么情况下索引会失效？
13. 是不是 6013xx18 外包？ 答 否
14. rabbitmq 如何保证消息消费幂等性?
15. 事务的隔离级别
16. mq 消费是 pull 还是push 有哪些方式持久化？用的哪个工作模式? topic
17. ioc 容器的启动过程？ 
18. springboot 的启动是如何实现的？ springboot 与spring的区别？


员工的价值是不能通过敬业度来衡量的，对于程序员来说更是如此，我们相信10倍程序员理论，高绩效程序员能通过优良的设计来高效的完成工作，而另外一些则会掉入焦油坑，投入大量的时间和精力而举步维艰，无法自拔。
以结果为导向，通过产出来衡量绩效是最有效的评估方法，但是我们并不否认高度敬业的价值，高度敬业依然是我们非常推崇的价值观，不过它只是我们达到高绩效的一个手段，而不是我们的行动的最终目标。
例如：索引优化：候选人掌握前缀原则，了解二级索引和主键索引的区别，但是不知道可以利用覆盖索引来避免回表。
```

![面经.jpg](https://i.loli.net/2021/08/15/huUHbVO9deP2Fqm.jpg)

# 参考

* [2021年中高级最全Java面试题](https://zhuanlan.zhihu.com/p/346994550)
* [atguigu尚硅谷面试题](http://www.atguigu.com/mst/java/gaopin/17832.html)
* [sakura-editor](https://github.com/sakura-editor/sakura)
* [理解浏览器渲染流程](https://www.liuxing.io/blog/how-browser-rendering-works/)
* [adblock-for-youtube](https://chrome.google.com/webstore/detail/adblock-for-youtube/cmedhionkhpnakcndndgjdbohmhepckk)
* [新一代基于UDP的低延时网络传输层协议——QUIC详解](https://www.ahfesco.com.cn/affairs/Article.asp?id=3466)
* [hashmap](https://howtodoinjava.com/java/collections/hashmap/how-hashmap-works-in-java/)
* [KPI 结果导向](http://tech.ipalfish.com/blog/2020/03/20/recruitment/)
* [面试SOP 标准流程](http://tech.ipalfish.com/blog/2020/04/01/interview/)
* [职级体系应该为员工提供明确的胜任标准](http://tech.ipalfish.com/blog/2020/06/07/profession/)
* [支付宝钱包系统架构内部剖析（架构图）](http://www.woshipm.com/pmd/160822.html)
* [支付系统如何进行分布式改造](https://blog.csdn.net/qq_27384769/article/details/80221007)