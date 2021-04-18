---
title: Pinpoint 安装部署教程
date: 2020-04-09 21:46:24
tags: life
---
Pinpoint  APM  Manual
---
# 背景
* 在日常开发过程中需要通过`APM(Application Performance Management)`工具来监控我们部署的应用服务是否稳定正常，目前常用的有`skywalking`和`pinpint`
  <!--more-->
 ![性能对比截图](http://skywalking.apache.org/assets/img/Pinpoint-PK-skywalking.184b13d4.png)

# 准备条件
* [CentOS 7](http://mirrors.aliyun.com/centos/7.7.1908/isos/x86_64/)
* [JDK1.8](https://injdk.cn/)
* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=mac)
* [Tomcat 9](https://mirror.olnevhost.net/pub/apache/tomcat/tomcat-9/v9.0.34/bin/apache-tomcat-9.0.34.tar.gz)
* [pinpoint 的源码包](https://github.com/naver/pinpoint/archive/v2.0.1.zip)
* [pinpoint-agent-2.0.1.tar.gz](https://github.com/naver/pinpoint/releases/download/v2.0.1/pinpoint-agent-2.0.1.tar.gz)  代理客户端
* [pinpoint-collector-2.0.1.war](https://github.com/naver/pinpoint/releases/download/v2.0.1/pinpoint-collector-2.0.1.war)
* [pinpoint-web-2.0.1.war](https://github.com/naver/pinpoint/releases/download/v2.0.1/pinpoint-web-2.0.1.war)
* [hbase 2.2.4](http://hbase.apache.org/downloads.html)
* [apache zooKeeper 3.6.0](https://downloads.apache.org/zookeeper/zookeeper-3.6.0/apache-zookeeper-3.6.0-bin.tar.gz) 
* 单机部署的话可以使用`hbase内置的zookeeper`如果使用外置的版本是`zookeeper3.5`以上需要注意会`默认占用8080端口做AdminServer`，你可以修改`zoo.cfg`来修改`admin.serverPort=9999`
# JDK安装配置
* 首先下载jdk，创建java 环境目录`mkdir -p /usr/local/java/`
* 解压 `tar -xvf jdk-8u202-linux-x64.tar.gz -C /usr/local/java/`
* 最后`vim /etc/profile` 添加

```shell
export JAVA_HOME=/usr/local/java/jdk1.8.0_202
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```

# Hbase环境配置
* 解压hbase  `tar -xvf hbase-2.2.4-bin.tar.gz`
* 修改hbase的配置文件`vim /root/hbase-2.2.4/conf/hbase-env.sh` 中添加一行

```
 配置java_home
 export JAVA_HOME=/usr/local/java/jdk1.8.0_202/
 使用外部zookeeper管理hbase
 export HBASE_MANAGES_ZK=flase
```



* 修改hbase的配置文件`vim /root/hbase-2.2.4/conf/hbase-site.xml` 中添加一行 

```
 <configuration>
      <property>
          <name>hbase.rootdir</name>
          <value>/root/hbase-2.2.4/data/hbase</value>
      </property>
      <property>
          <name>hbase.zookeeper.property.dataDir</name>
          <value>/root/hbase-2.2.4/data/zookeeper</value>
      </property>
      <property>
          <name>hbase.master.port</name>
          <value>60000</value>
      </property>
      <property>
          <name>hbase.regionserver.port</name>
          <value>60020</value>
      </property>
  </configuration>
* 启动hbase  `cd /root/hbase-2.2.4/bin` 并执行`./start-hbase.sh`
* 打开 `http://127.0.0.1:16010/master-status` 查看hbase web管控台
* 导入pinpoint到hbase 即解压`pinpoint-2.0.1.tar.gz`后得到`/root/pinpoint-2.0.1/hbase/scripts/hbase-create.hbase `的sql文件并拷贝到`hbase的bin目录`
* `./root/hbase-2.2.4/bin/hbase shell hbase-create.hbase `执行之后可以`status 'detailed'`查看数据
```
* 在浏览器打开查看hbase http://10.211.55.4:16010/master-status

  ![Xnip2020-04-12_19-34-34_hbase.jpg](https://i.loli.net/2020/04/12/lhTfJuGYrSxb146.jpg)

# 配置web和collector

  ```
* 解压tomcat `tar -xvf apache-tomcat-9.0.34.tar.gz`并重命名`cp -R apache-tomcat-9.0.34 web`
* 修改tomcat 的默认端口号`vim /root/web/conf/server.xml`找到8080 修改成5050
* 将web部署到webapps里面重命名为ROOT.war `cp -R pinpoint-web-2.0.1.war /root/web/webapps/ROOT.war`
* 启动tomcat  `进入cd /root/web/bin`目录并执行`./startup.sh `
* 修改ROOT.war 默认配置文件绑定zookeeper的实际地址
  /进入目录 
  cd web/webapps/ROOT/WEB-INF/classes/
  修改hbase.properties中 
  hbase.client.host=127.0.0.1
  修改pinpoint-web.properties 中
  pinpoint.zookeeper.address=127.0.0.1
  进入目录
  cd /root/web/webapps/ROOT/WEB-INF/classes/profiles/local
  修改hbase-env.properties 
  hbase.client.host=127.0.0.1
  修改pinpoint-web-env.properties 
  pinpoint.zookeeper.address=127.0.0.1
  进入目录
  cd /root/web/webapps/ROOT/WEB-INF/classes/profiles/release
  修改hbase-env.properties 
  hbase.client.host=127.0.0.1
  修改pinpoint-web-env.properties 
  pinpoint.zookeeper.address=127.0.0.1
* collector基本和web配置步骤一致
  ```
# 配置agent
```shell
* 打包springboot项目并整合agent参数`java -javaagent:/root/pinpoint-agent-2.0.1/pinpoint-bootstrap-2.0.1.jar -Dpinpoint.agentId=应用服务名id -Dpinpoint.applicationName=应用服务名id -jar mongodb-1.0.jar`
java -javaagent:/root/pinpoint-agent-2.0.1/pinpoint-bootstrap-2.0.1.jar -Dpinpoint.agentId=codewindy-mongodb -Dpinpoint.applicationName=codewindy-mongodb -jar mongodb-1.0.jar
```
![Xnip2020-04-12_20-05-48pinpoint_init.jpg](https://i.loli.net/2020/04/12/mvInzeOaYPy4QXf.jpg)
![Xnip2020-04-12_20-06-41_pinpoint_callTree.jpg](https://i.loli.net/2020/04/12/mCT5Esp69j3DrlS.jpg)
# 参考

* [pinpoint 安装部署教程](https://github.com/ameizi/DevArticles/issues/166)
* [pinpoint-agent-collector -web下载](https://github.com/naver/pinpoint/releases)
* [skywalking vs pinpoint](http://skywalking.apache.org/zh/blog/2019-02-24-skywalking-pk-pinpoint.html)
* [pinpoint原理和docker部署](http://www.tangrui.net/2016/pinpoint-plugin-development.html)
