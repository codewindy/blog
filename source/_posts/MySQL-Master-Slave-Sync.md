---
title: MySQL-Master-Slave-Sync
date: 2016-05-12 16:38:45
tags: MySQL
---

**TODO_LIST**
1. springmvc 的servlet启动参数 关系
2. 整理笔记上线到网上blog

    ```
        springmvc的servlet 开机启动配置
        load-on-startup标记容器是否在启动的时候实例化并调用其init()方法的优先级。
        它的值表示servlet应该被载入的顺序
        当值为0或者大于0时，表示容器在应用启动时就加载并初始化这个servlet；
        如果值小于0或未指定时，则表示只有在第一次请求的容器才在该servlet调用初始化函数
        正值越小，servlet的优先级越高，应用启动时就越先加载。
        值相同时，容器就会自己选择顺序来加载。
        SELECT NOW(); 选择现在的时间在redis 里面设置一个倒计时做秒单

    ```
3. mysql读写分离的背景介绍
    ```
        服务器压力大,需要读写分离,负载均衡
        主服务器通过写入bin-log文件实现[通过授权在master 服务器创建帐号]传递给slave 服务器读取
        从服务器读 bin-log 写
         Relay_Master_Log_File: mysql-bin.000050
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
    ```

一 .mysql 主从同步linux和window配置(前提概要实现主从同步,读写分离)
---
<!--  more  -->


1. 配置mysql 允许远程访问
    ```
    先登录到mysql服务器 授予root 远程登录权限
    grant all on *.* to 'root'@'%' identified by 'root' with grant option;
    flush privileges;
    ```
2. 主从同步配置前提
    1. 数据库版本要相同
    2. 数据库要相同taotao.sql
    3. 主从服务器id 要设置不同,可以跟ip 后缀相同好记忆


    ```
    vim /etc/my.cnf 配置文件

    # Replication Master Server (default)
    # binary logging is required for replication

    log-bin=mysql3306master-bin  名字可以自定义
    #log-bin=mysql-bin
    # binary logging format - mixed recommended
    配置需要同步的数据库名taotao,不配置默认同步所有
    binlog-do-db=taotao


    # required unique id between 1 and 2^32 - 1
    # defaults to 1 if master-host is not set
    # but will not function as a master if omitted
    server-id   = 203  一定要配置和ip 地址可以一致

    ```
3. 重启mysql 服务  service mysql restart
4. 登录到 mysql -uroot -proot    show master status 查看主mysql 服务的状态信息
5. 在主mysql 创建从slave账户并授权slave

    ```
    show master status;
    grant replication slave on *.* to 'slave01'@'%' identified by '123456'; flush privileges;
    show master status;  显示master bin-log文件名和位置
    ```

5. 在从服务器中配置my.cnf 中配置server-id = 206  配置从服务器的ip相同
6. 然后在slave 中执行
        ```
        CHANGE MASTER TO
            master_host='192.168.25.203',
            master_user='slave01',
            master_password='123456',
            master_port=3306,
            master_log_file='mysql-bin.000002',//从主配置中查询出来的会变的
            master_log_pos=420; //每次都会改变的

        start slave; //启动从库去请求主库
        show slave status;  //查看 slave_IO Runing  NO 下面要改debug成  yes
            //出现错误 查看log从服务器的mysql.err.log 错误日志的uuid 一定要不重复

        查找到配置文件auto.cnf 中  server-uuid=uuid 改成不同的即可.
        service mysql restart
        stop slave;
        start slave; 开启从服务器请求同步  reset slave; 重置
        ```





二 .mysql主从全部配置的流程
---
-  [前提: 注意主从之间的库名要一致,版本一致,server-id不同,见下图操作实例,和代码示例]


-  ![mysql 架构原理实现图](http://ot2hu9qoc.bkt.clouddn.com/17-7-27/86344959.jpg)
-  ![先配置主master服务器的my.cnf文件](http://ot2hu9qoc.bkt.clouddn.com/17-7-27/81757402.jpg)
-  ![查看master服务器是否正常启动](http://ot2hu9qoc.bkt.clouddn.com/17-7-27/14730145.jpg)
-  ![在naviacat中查看和处于安全考虑在master中添加slave01帐号和密码](http://ot2hu9qoc.bkt.clouddn.com/17-7-27/76878922.jpg)
-  ![再次查看master服务器的状态](http://ot2hu9qoc.bkt.clouddn.com/17-7-27/64397842.jpg)
-  ![从服务器slave的配置my.cnf中设置server-id需要和master的id不同](http://ot2hu9qoc.bkt.clouddn.com/17-7-27/85699377.jpg)
-  ![在从服务器中执行以下语句](http://ot2hu9qoc.bkt.clouddn.com/17-7-27/73406735.jpg)
-  ![查看是否实现主从同步的标志Yes](http://ot2hu9qoc.bkt.clouddn.com/17-7-27/81016323.jpg)
-  ![MySQL图解WHERE](http://ot2hu9qoc.bkt.clouddn.com/%E7%8E%8B%E8%88%AA%E5%A8%81-MySQL-where%E6%9D%A1%E4%BB%B6%E5%89%96%E6%9E%90.jpg)

        ```
        -----------------1 前提配置----------------------------
        两边的mysql 服务器能够ping 通 并且 service iptables stop 或者开放端口
        setenforce 0
        master 主表的 ip 192.168.25.203

        slave  从表的ip  192.168.25.206

        -----------2  master配置的流程-----------

        vim /etc/my.cnf 配置文件

            vim /etc/my.cnf 配置文件

                    # Replication Master Server (default)
                    # binary logging is required for replication

                    log-bin=mysql3306master-bin  //名字可以自定义
                    #log-bin=mysql-bin
                    # binary logging format - mixed recommended 防止主键重复
                    skip-name-resolve
                    binlog-do-db=taotao //配置需要同步的数据库名taotao,多个数据库就复制一行添加,不配置默认同步所有


                    # required unique id between 1 and 2^32 - 1
                    # defaults to 1 if master-host is not set
                    # but will not function as a master if omitted
                    server-id   = 203 // 一定要配置和ip 地址可以一致


        grant all on *.* to 'root'@'%' identified by 'root' with grant option;
        flush privileges;    //一定可以让远程访问,即navicat访问



        grant replication slave on *.* to 'slave01'@'%' identified by '123456'; flush privileges;
        show master status;



        show master status;



        grant replication slave on *.* to 'slave01'@'%' identified by '123456'; flush privileges;
        show master status;

        show GRANTS for 'slave01'@'%';


        -----------3  从表的配置--------------
                vim /etc/my.cnf 配置文件


                    # required unique id between 1 and 2^32 - 1
                    # defaults to 1 if master-host is not set
                    # but will not function as a master if omitted
                    server-id   = 206 // 一定要配置和ip 地址可以一致
                    skip-name-resolve #跳过dns检查

        grant all on *.* to 'root'@'%' identified by 'root' with grant option;
        flush privileges;    //一定可以让远程访问,即navicat访问



        CHANGE MASTER TO
                master_host='192.168.25.203',
                master_user='slave01',
                master_password='123456',
                master_port=3306,
                master_log_file='mysql-bin.000049',
                master_log_pos=107;
            stop slave ;
            start slave;
            show slave status;
            start slave;  reset slave;

            window下面查看是否是一致 runing 如果不是就是uuid 错误
            server-id=` ip 地址后面 `千万不要相同
            授权给从slave一定可以访问主master 的mysql 表,注意后面的%
                grant replication slave on *.* to 'slave01'@'%' identified by '123456'; flush privileges;
                show master status;

            reset slave; 重置从服务器,配置到此结束,下面为具体解释







                server-id=1        #数据库唯一ID，主从的标识号绝对不能重复。
                log-bin=mysql-bin    #开启bin-log，并指定文件目录和文件名前缀
                binlog-do-db=taotao  #需要同步的数据库。如果是多个同步库，就以此格式另写几行即可。如果不指明对某个具体库同步，就去掉此行，表示同步所有库（除了ignore忽略的库）。
                binlog-ignore-db=mysql  #不同步mysql系统数据库。如果是多个不同步库，就以此格式另写几行；也可以在一行，中间逗号隔开。
                sync_binlog = 1      ＃确保binlog日志写入后与硬盘同步
                binlog_checksum = none  ＃跳过现有的采用checksum的事件，mysql5.6.5以后的版本中binlog_checksum=crc32,而低版本都是binlog_checksum=none
                binlog_format = mixed   ＃bin-log日志文件格式，设置为MIXED可以防止主键重复。

                skip-name-resolve   #不把IP地址解析为主机名; 与访问控制(mysql.user数据表)有关的检查全部通过IP地址行进。

                mysql优化之--skip-name-resolve


                同一IDC ,IDC内部有DNS服务器，对各服务器的IP做了反向解析，
                但未对内网IP做反向解析，所以使用skip-name-resolve以后用内网地址向mysqlslap请求响应快了一半




                涉及参数 --skip-name-resolve ,--skip-host-cache ,--skip-networking

                当新的客户连接mysqld时，mysqld创建一个新的线程来处理请求。该线程先检查是否主机名在主机名缓存中。如果不在，线程试图解析主机名：

                ·         如果操作系统支持线程安全gethostbyaddr_r ()和gethostbyname_r()调用，线程使用它们来执行主机名解析。

                ·         如果操作系统不支持线程安全调用，线程锁定一个互斥体并调用gethostbyaddr()和gethostbyname()。在这种情况下，在第1个线程解锁互斥体前，没有其它线程可以解析不在主机名缓存中的主机名。

                你可以用--skip-name-resolve选项启动mysqld来禁用DNS主机名查找。然而，在这种情况下，你只可以使用MySQL中的授权表中的IP号。

                如果你有一个很慢的DNS和许多主机，你可以通过用--skip-name-resolve禁用DNS查找或增加HOST_CACHE_SIZE定义(默认值：128)并重新编译mysqld来提高性能。

                你可以用--skip-host-cache选项启动服务器来禁用主机名缓存。要想清除主机名缓存，执行FLUSH HOSTS语句或执行mysqladmin flush-hosts命令。

                如果你想要完全禁止TCP/IP连接，用--skip-networking选项启动mysqld。




                连接mysql时，都会向DNS做反向地址查询
                只有等超时失败后，mysql才会响应客户端
                等待解析的mysql进程都是 login状态

        ```

三 . 通过spring 配置文件和具体代码实现
---

-  通过spring AOP 动态切换datasource数据源和slow_query_log 慢查询日志记录
-  读[从slave服务器的配置],增删改[master服务器的配置]读写分离.
1. 待续...中

# 参考
* [MySQL Manual reference](http://ot2hu9qoc.bkt.clouddn.com/refman-5.7-en.a4.pdf)