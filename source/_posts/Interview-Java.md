---
title: Java 面试题
date: 2021-08-30 23:29:26
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

魔点科技 一面

    一：为什么重写了equals方法, 同时要重写hashcode方法
    二：线程池的核心参数
    三：线程池关闭方法，为什么
    四：dubbo如何实现一个拦截器，spi机制如何激活拦截类
    五：dubbo的版本有什么用，dubbo的group了解过吗，dubbo的负载均衡策略是怎样的
    六：jvm的内存模型是怎样的？jdk8出现的元空间对比方法区有什么区别，有什么优点吗？你说可以直接操作内存，独立于jvm之外，那如果进程挂掉了，会不会被回收呢？元空间有那两块区域，栈里面的数据结构是怎样的，是线程私有还是公有，新生代和老年代比例是多少
    七：jdk的动态代理 和 cglib有什么区别
    八：如何排查cpu占用过高的原因是什么导致的，那一台服务器上，如何排查进程中的是哪个线程占用过高的情况
    九：mysql的事务了解吗

员工的价值是不能通过敬业度来衡量的，对于程序员来说更是如此，我们相信10倍程序员理论，高绩效程序员能通过优良的设计来高效的完成工作，而另外一些则会掉入焦油坑，投入大量的时间和精力而举步维艰，无法自拔。
以结果为导向，通过产出来衡量绩效是最有效的评估方法，但是我们并不否认高度敬业的价值，高度敬业依然是我们非常推崇的价值观，不过它只是我们达到高绩效的一个手段，而不是我们的行动的最终目标。
例如：索引优化：候选人掌握前缀原则，了解二级索引和主键索引的区别，但是不知道可以利用覆盖索引来避免回表。

以下内容摘抄于网络搜索
    开始
    说实话，我去年三月份连mvcc是啥都不知道，更别说共享表空间，bean的生命周期听都没听过。
    然后开始看视频看文章又学了一年，自己看源码贼痛苦，又浪费时间，但是看了源码，真的后面贼香，看到一些大牛在项目写的代码已经自己也能写了，像一些bfpp在项目中的应用，项目中使用设计模式，真的策略模式贼香。
    然后学习过程中要做笔记，记录学到的，学不懂的，学好了，面试题面经都只是让你记起知识点。少玩游戏好好学习。
    公司
群接龙
    锁升级的过程
    线程池参数
    synz和lock的区别
    cas
    总线风暴
    总线锁和mesi
    mvcc，b+，慢sql优化
    jvm优化，老年代和年轻代，对象什么时候进入老年代，cms，什么时候老年代回收，jvm参数
    spring boot自动装配的原理
    eureka心跳机制
    select，poll，epoll区别实现
    redis集群如果一个主和下面的从都宕机了，集群还提供服务么
    荔枝
    mvcc，b+
    redis持久化，集群
    jvm内存结构，回收算法，垃圾回收器，jvm调优
    mysql慢查询优化
    线程池，锁
    分布式锁
    eureka
    mq
    spring源码
美团
    spring聊源码，bean的生命周期，三级缓存
    分布式事务
    分布式id生成雪花算法原理
    豪猪限流
    什么时候适合信号量限流
    什么时候适合线程限流
    令牌桶和漏斗的区别
    支付转账怎么保证一致性
    住数据库连接超了，主服务不可用，怎么快速解决问题，让服务可以写数据
    算法：手写lru，一个简单的for循环，实现负载均衡rpc调用
酷狗
    cpu一致性缓存协议
    mysql，mvcc，事务隔离级别，redo log和undo log
    一致性和持久性怎么保证
    双写共享表空间
    主从复制，半同步，全同步
    redis，集群，主从节点挂了怎么保证高可用
    zk，eureka的区别，为啥不用zk做注册中心
    mq，怎么保证消息可用，recketmq事务实现，消息存储，b挂了消息还能被读到么
    分布式事务
    
    20210906 微众银行面试
    输出二叉树每层最左侧节点：
      4 6 3 9
           4
     6            5
        3      7    8
                        9
    字节一面
      插入排序： 【5, 4, 2, 8, 7, 9】
    
    20210914 字节二面
    leetcode
       【2,3,5】 k= 8
       return 
       【2,2,2,2】
       【3,5】
     https://www.yuque.com/renyong-jmovm/ds/vepkxm   面试题笔记整理
     https://www.yuque.com/renyong-jmovm/ds/svgzsk
     https://www.yuque.com/renyong-jmovm/ds/eqyugo
    
     20210916 卓正医疗
     算法题
     two sum
     maxnumgap
    
    20210918 商汤科技 二面
    
    在15G文本中取出top10的单词 功能设计
    
    20210918 商汤科技 三面

    在1亿人脸 256 浮点数一张脸数据中取top k的值 功能设计
总结
    简历要写好一点，不然面试机会都没有，去大厂不要包装工作经验，如果要跳槽，请至少提前一个月准备，我是去年10月份就有今年跳槽的想法，然后开始学习，跳槽前一个月我都还在学习，前一个月是分模块准备面试题，不会的模块就学好了再答题。
    面试问的内容无非是看你简历写的东西问，我写了spring源码，问我的公司还是比较少的，大部分是问线程、锁、mysql、redis、jvm。spring是很少问，算法其实真的不准备也是可以的，问算法的公司还是挺少，不过年限大的估计问算法的公司会多。
    刚开始面试先找小公司，两家差不多就可以了，多了感觉有点浪费时间，因为每家都差不多三面，是挺累的，然后紧张的就多面，我刚面第一家贼紧张，第二家就开始好点，后面就已经觉得自己说的贼溜。
    面试题答案记不住的，找个人面面你，你会忘不掉的。
    然后加油，两年之后再继续新的征程。
```

![面经.jpg](https://i.loli.net/2021/08/15/huUHbVO9deP2Fqm.jpg)

# 参考

* [2021年中高级最全Java面试题](https://zhuanlan.zhihu.com/p/346994550)
* [atguigu尚硅谷面试题](http://www.atguigu.com/mst/java/gaopin/17832.html)
* [sakura-editor](https://github.com/sakura-editor/sakura)
* [理解浏览器渲染流程](https://www.liuxing.io/blog/how-browser-rendering-works/)
* [adblock-for-youtube](https://chrome.google.com/webstore/detail/adblock-for-youtube/cmedhionkhpnakcndndgjdbohmhepckk)
* [新一代基于UDP的低延时网络传输层协议——QUIC详解](https://www.ahf****esco.com.cn/affairs/Article.asp?id=3466)
* [hashmap](https://howtodoinjava.com/java/collections/hashmap/how-hashmap-works-in-java/)
* [KPI 结果导向](http://tech.ipalfish.com/blog/2020/03/20/recruitment/)
* [面试SOP 标准流程](http://tech.ipalfish.com/blog/2020/04/01/interview/)
* [职级体系应该为员工提供明确的胜任标准](http://tech.ipalfish.com/blog/2020/06/07/profession/)
* [支付宝钱包系统架构内部剖析（架构图）](http://www.woshipm.com/pmd/160822.html)
* [支付系统如何进行分布式改造](https://blog.csdn.net/qq_27384769/article/details/80221007)
* [面试必问的线程池计算coresize](https://mp.weixin.qq.com/s/v3eClGAgC7iDW09MoDKiEA)
* [difference-between-synchronized-and-volatile](https://somersames.xyz/2021/05/30/difference-between-synchronized-and-volatile/)
* [力扣算法题lenovobenben](https://github.com/lenovobenben/wen)
* [阿里面试视频](https://www.bilibili.com/video/BV1kU4y1574X?p=23)  阿里面试视频
* [面试笔记](https://github.com/jujunchen/Java-interview-question/blob/master/4.JVM.md) 面试笔记
* [Java-interview-question](https://github.com/jujunchen/Java-interview-question/blob/master/11.Spring%20Boot.md)
* [renyong-jmovm面试题笔记整理](https://www.yuque.com/renyong-jmovm/ds/vepkxm)   210道面试题笔记整理
* [lexiangqizhong面试题](https://www.yuque.com/lexiangqizhong/java/anqlsy)
* [ppt 分享](https://github.com/rfyiamcool/notes/blob/main/git.md) ppt
* [ld246.com](https://ld246.com/article/1624867520367)
* [ld246.com](https://ld246.com/article/1622557526714)
* [ld246.com](https://blog.csdn.net/qq193423571/article/details/81637075)
* [woshimaxiao1](https://blog.csdn.net/woshimaxiao1/article/details/83661464)
* [chengkui1990](https://blog.csdn.net/chengkui1990/article/details/85253328)
* [java-thread-x-juc-AtomicInteger](https://www.pdai.tech/md/java/thread/java-thread-x-juc-AtomicInteger.html)
* [macrozheng 技术笔记](http://www.macrozheng.com/#/README)
* [面经和离职吐槽duibiao](https://duibiao.info/category/6)
* [210道面试题笔记整理pdf下载](https://github.com/codewindy/win-toolbak/blob/master/interview210_yuque-zgpx1l.pdf)
* [ikays.im](https://ikays.im/category/%e7%bb%bc%e5%90%88%e6%95%99%e7%a8%8b) Java资源
* [huzb.me](http://huzb.me/2021/08/21/%E9%9B%B6%E6%95%A3%E9%97%AE%E9%A2%98%E6%B1%87%E6%80%BB/)
* [Spring-data-jpa和mybatis的比较](https://www.cnblogs.com/mr-wuxiansheng/p/14491139.html)
* [Java知识整理](https://www.yuque.com/fcant)
* [arthas 教程](https://www.sunshanpeng.com/tags/arthas/)  [工商银行打造在线诊断平台的探索与实践.md](https://github.com/lyghzh/pub/blob/main/doc/%E5%B7%A5%E5%95%86%E9%93%B6%E8%A1%8C%E6%89%93%E9%80%A0%E5%9C%A8%E7%BA%BF%E8%AF%8A%E6%96%AD%E5%B9%B3%E5%8F%B0%E7%9A%84%E6%8E%A2%E7%B4%A2%E4%B8%8E%E5%AE%9E%E8%B7%B5.md)
* [ 3-5 年 Java 的面试资源](https://www.v2ex.com/t/850159)
* 以下是面试题和算法题

* https://manbucoding.com/travel-coding/leetcode/001_%E4%B8%A4%E6%95%B0%E4%B9%8B%E5%92%8C.html
* http://www.jayh.club/#/README
* http://notfound9.github.io/interviewGuide/#/
* https://www.pdai.tech/
* https://hadyang.com/interview/docs/basic/
* https://osjobs.net/topk/all/
* https://doocs.gitee.io/advanced-java/#/
* https://snailclimb.gitee.io/javaguide/#/
* https://github.com/AobingJava/JavaFamily
* https://github.com/javagrowing/JGrowing/blob/master/JGrade.png
* https://github.com/CyC2018/CS-Notes
* https://github.com/xingshaocheng/architect-awesome
* https://github.com/allentofight/easy-cs

