---
title: ElasticSearch 7.9.0 环境搭建以及使用部署
date: 2021-08-08 16:00:56
tags: java
---

## ELK Docker环境搭建以及部署

# 背景
* ElasticSearch 7.9.0  安装部署以及使用
<!--more-->
# 步骤
* docker 安装

  ```shell
  wget https://get.docker.com/gpg
  rpmkeys --import ./gpg
  curl -sSL https://get.daocloud.io/docker | sh
  sudo systemctl restart docker
  vim /etc/docker/daemon.json
  {
    "registry-mirrors": [
      "https://hub-mirror.c.163.com",
      "https://mirror.baidubce.com"
    ]
  }
  
  $ sudo systemctl daemon-reload
  $ sudo systemctl restart docker
  ```

* docker-compose 安装

  ```
  $ sudo curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
  
  # 国内用户可以使用以下方式加快下载
  $ sudo curl -L https://download.fastgit.org/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
  
  $ sudo chmod +x /usr/local/bin/docker-compose
  
  sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
  
  docker-compose --version
  ```

* docker-compose.yml

  ```
  version: "3.1"
  services:
    elasticsearch:
  	image: daocloud.io/library/elasticsearch:7.9.0
  	restart: always
  	container_name: elasticsearch
  	ports:
  	  - 9200:9200
  	environment:
        - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
        - discovery.type=single-node
    kibana:
  	image: daocloud.io/library/kibana:7.9.0
  	restart: always
  	container_name: kibana
  	ports:
  	  - 5601:5601
  	environment:
  	  - elasticsearch_url=http://192.168.106.129:9200
  	depends_on:
  	  - elasticsearch
  ```

* 启动docker-compose 失败后问题解决

  ```
  ERROR: [2] bootstrap checks failed
  [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
  [2]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
  
  在 /etc/sysctl.conf文件最后添加一行
  vm.max_map_count=262144
  sysctl -p
  
  进入容器config/elasticsearch.yml中修改 discovery.seed_hosts: ["127.0.0.1", "[::1]"]
  ```

* ![image-20210804223814572.png](https://i.loli.net/2021/08/08/t4Uuj8hTvMzcxPp.png)

* http://192.168.106.129:9200/_cat/health?pretty  查询节点状态

* 安装ik分词器

  ```
  跟es版本对应安装记住 通过http 方式安装可以自己用nginx 服务或者everything 搭建
  ./es/bin/elasticsearch-plugin install http://xxx.elasticsearch-analysis-ik-7.9.0.zip -v
  docker restart es_containerId
  
  POST _analyze
  {
    "analyzer":"ik_max_word",
    "text":"东方财富中国平安"
  }
  ```

# 参考
* [elasticsearch 中文社区](https://elasticsearch.cn/download/) 下载elasticsearch 以及kibana和ik-analyzer
* [elasticsearch 语雀详细文档](https://www.yuque.com/susuxuechang/rwicpv) 语雀以及看云kancloud文档
* [elasticsearch-analysis-ik](https://github.com/medcl/elasticsearch-analysis-ik)
* [docker和docker-compose安装](https://www.runoob.com/docker/docker-compose.html)
* [Elasticsearch7使用详解](https://rstyro.github.io/blog/2020/09/10/Elasticsearch7%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3/)

