---
title: springboot-elk-6.5.4
date: 2020-11-04 12:19:26
tags: java
---

## springboot整合elk 并安装elasticsearch-analysis-ik分词器插件
# 一. 背景
* 业务中需要使用`ik分词`进行文章关键字搜索来查询对应的数据
<!--more-->
# 二. 步骤
* 在服务上首先安装使用docker和 docker-compose
* 根据最后面的docker-compose.yml拉取镜像创建服务器
* 修改服务器配置
  > docker-compose up -d 启动
  >
  > docker-compose logs -f 查看日志
  >
  > sudo sysctl -w vm.max_map_count=262144  **修改/etc/sysctl.conf 不然报错启动不来**
* 安装分词器插件
  > http://tomcat01.qfjava.cn:81/elasticsearch-analysis-ik-6.5.4.zip 加速下载包
  > 
  > 执行命令安装 ./elasticsearch-plugin install  http://tomcat01.qfjava.cn:81/elasticsearch-analysis-ik-6.5.4.zip
* `注意安装分词插件后需要重启服务器才能生效`
***
  ```yml
      version: "3.1"
      services:
      elasticsearch:
      image: daocloud.io/library/elasticsearch:6.5.4
      restart: always
      container_name: elasticsearch
      ports:
      - 9200:9200
      kibana:
      image: daocloud.io/library/kibana:6.5.4
      restart: always
      container_name: kibana
      ports:
      - 5601:5601
      environment:
      - elasticsearch_url=http://10.211.55.8:9200
      depends_on:
      - elasticsearch
```
  ***
# 三. 参考
* [elasticsearch-analysis-ik](https://github.com/medcl/elasticsearch-analysis-ik)
* [docker和docker-compose安装](https://www.runoob.com/docker/docker-compose.html)
* [Elasticsearch7使用详解](https://rstyro.github.io/blog/2020/09/10/Elasticsearch7%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3/)