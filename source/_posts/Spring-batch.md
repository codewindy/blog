---
title: Spring batch 批量金融业务应用
date: 2022-05-08 13:49:06
tags: java
---
# Spring batch 知识总结
* spring batch 架构图
  <!--more-->

* ![img](https://user-images.githubusercontent.com/15072465/167286719-272181e7-8160-44dd-9d9c-2ee695c2335b.png)

   ## Spring Batch 简介

   > Spring Batch提供了可重复使用的功能，这些功能对于处理大量记录至关重要，包括记录/跟踪，事务管理，作业处理统计，作业重启，跳过和资源管理。它还提供更高级的技术服务和功能，通过优化和分区技术实现极高容量和高性能的批处理作业。简单和复杂的大批量批处理作业可以高度可扩展的方式利用框架来处理大量信息。

   ## 什么是批处理

   > 现代互联网企业、金融业、电信业甚至传统行业通过OLTP（联机事务处理）的业务系统积累了海量企业数据，需要企业应用能够在关键任务中进行批量处理来操作业务逻辑。通常情况下，此类业务并不需要人工参与就能够自动高效地进行复杂数据处理与分析。例如，定期对大批量数据进行业务处理（如银行对账和利率调整、或者跨系统的数据同步），或者是把从内部和外部系统中获取到的数据进行处理后集成到其他系统中去，这类工作被称之为“批处理”。“批处理”工作在面对复杂的业务以及海量的数据处理时，无需人工干预，仅需定期读入批量数据，然后完成相应业务处理并进行归档操作。

   ## Spring Batch 优势

   1. SpringBatch框架通过提供丰富的即开即用的组件和高可靠性、高扩展性的能力，使得开发批处理应用的人员专注于业务的处理，提升批处理应用的开发效率，通过SpringBatch可以快速地构建出轻量级的健壮的并行处理应用。
   2. 批处理开发人员使用Spring编程模型：专注于业务逻辑，让框架负责基础架构
   3. 提供通用的核心执行服务作为所有项目可以实现的接口
   4. 清楚地分离基础架构，批处理执行环境和批处理应用程序之间的关注点
   5. 提供一个简单的部署模型，使用Maven构建的架构JAR与应用程序完全分离

# Spring Batch 使用场景

   > 一般的典型批处理程序

   1. 从数据库，文件或队列中读取大量记录
   2. 以某种方式处理数据
   3. 以修改的形式写回数据

   > Spring Batch 业务场景

   1. 定期提交批处理
   2. 并发批处理：并行处理作业
   3. 分阶段的企业消息驱动处理
   4. 大规模并行批处理
   5. 失败后手动或预定重启
   6. 依赖步骤的顺序处理（使用扩展的toworkflow驱动批次）
   7. 部分处理：跳过记录（例如，回滚时）
   8. 整批交易，适用于批量较小或现有存储过程/脚本的情况
# 关键词解析
  *  批处理的典型概念模型，其设计非常精简的十个概念，完整支撑了整个框架：

      | 关键词         | 描述                                                    |
      | -------------- | ------------------------------------------------------- |
      | Job Repository | 基础组件，用来持续化 Job 的元数据，默认使用内存         |
      | Job Laucher    | 基础组件，用来启动 Job                                  |
      | Job            | 应用组件，是用来执行 Batch 操作的基础执行单元           |
      | Step           | Job 的一个阶段，Job由一组Step构成                       |
      | Tasklet        | Step的一个事务过程，包含重复执行，同步，异步等策略      |
      | Item           | 从数据源读出或写入的一条数据                            |
      | Chunk          | 给定数量的 Item 集合                                    |
      | Item Reader    | 从给定的数据源读取 Item 集合                            |
      | Item Processor | 在 Item 写入数据源之前进行数据清洗 （转换，校验，过滤） |
      | Item Writer    | 把 Chunk 中包含的 Item 写入数据源                       |
      
      ![img](https://klose911.github.io/html/material/pic/job_step.jpg)
      ![img](https://klose911.github.io/html/material/pic/step_execution.jpg)

# 参考

* https://www.jdon.com/springboot/spring-batch-trivadis.html

* **https://klose911.github.io/html/material/spring_batch.html 笔记很全**

* https://adongs.com/articles/2019/08/30/1567132951228.html

* https://howtodoinjava.com/spring-batch/spring-batch-step-partitioning/

* https://hevodata.com/learn/spring-batch-parallel-processing/

  
