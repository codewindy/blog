---
title: springboot-redis-tips
date: 2020-11-20 08:23:57
tags: redis
---

# 一. 背景

*  `springboot2.x` 之后默认redis客户端不是`jedis`而是`lettuce`但是在集群环境中如果有一个节点挂了就会导致服务不可用
<!--more-->

# 二. 解决
```java
# Example 37. Enabling periodic cluster topology view updates
RedisClusterClient clusterClient = RedisClusterClient.create(RedisURI.create("localhost", 6379));


ClusterTopologyRefreshOptions topologyRefreshOptions = ClusterTopologyRefreshOptions.builder()
                                .enableAdaptiveRefreshTrigger(RefreshTrigger.MOVED_REDIRECT, RefreshTrigger.PERSISTENT_RECONNECTS)
                                .adaptiveRefreshTriggersTimeout(30, TimeUnit.SECONDS)
                                .build();

clusterClient.setOptions(ClusterClientOptions.builder()
                                .topologyRefreshOptions(topologyRefreshOptions)
                                .build());
...

clusterClient.shutdown();

```
# 三. 参考
* [redis整合letture自动拓扑更新](https://www.colabug.com/2020/0107/6815444/)
* [SpringBoot2.X与redis Lettuce集成踩坑](https://juejin.cn/post/6844904039096778759)