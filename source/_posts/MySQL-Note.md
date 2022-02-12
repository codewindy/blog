---
title: MySQL 开发规范笔记
date: 2022-02-12 15:37:52
tags:
---

## 表结构设计

1、每张表都必须有三个字段：`id`，`gmt_create`，`gmt_modify`，代表主键ID，记录创建时间，记录修改时间。

- `id`：必须是唯一并且递增的非负数数字类型，数据量小并发写入量不高用数据库自增Id，并发写入高或者数据量大有分表需求用雪花算法等id生成器生成。

> ```sql
> `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'id'
> ```

- `gmt_create`：顺序递增，存储每条记录的创建时间，一般场景用`datatime`类型，有跨时区的需求用`timestamp`类型。

> ```sql
> `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间'
> ```

- `gmt_modify`：存储每条记录的修改时间，一般场景用`datatime`类型，有跨时区的需求用`timestamp`类型。

> ```sql
> `gmt_modify` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间'
> ```

2、没有特殊需求都使用`innodb`存储引擎，并设置`id`为主键索引`PRIMARY KEY`。

> ```sql
> PRIMARY KEY (`id`)
> ```

		因为`id`字段是`innodb`的聚簇索引，使用`id`查询记录的速度最快。

3、主键`id`字段的长度不宜太大，因为二级索引都会存储主键的值，而且值越大，比较大小就越耗时。

> 一般用无符号自增主键`int(10)`即可，最大值`4,294,567,294‬`（`2,147,283,647‬*2`）。需要存储超过该长度的记录可以用`bigint(20)`。其中`int(N)`中的N只是显示长度，详见：<https://dev.mysql.com/doc/refman/8.0/en/integer-types.html>。

4、数据量大的表不要物理删除，使用字段`is_deleted`来做逻辑删除，1 表示删除，0 表示未删除。

> `innodb`在磁盘中是分页存储的，如果在记录中间插入或者删除数据都要移动后面的数据。
>
> 重要数据也可以使用逻辑删除。
>
> 逻辑删除不适合有唯一约束的表。

5、需要并发修改的数据使用`version`字段做乐观锁，每次修改都对版本号`+1`。

> 比如库存、余额等。

6、 长度超过 `2000` 字符的大字段用`blob`、`text`类型，并和主记录分开存储，可以提高查询速度。

> `mysql`已页为单位存储，每个页的大小为`16k`，如果单条记录内容太大则会影响范围查询。

7、如果存的都是数字就不要用字符类型的字段，存储空间和排序比较时数字都优于字符串。

8、数字类型如果不存负数就用无符号`unsigned`,可以增加存储大小。

> int(10)：-2,147,283,647‬~2,147,283,646
>
> int(10) unsigned：0~4,294,567,294

9、 小数类型为 decimal，禁止使用 float 和 double。

>  float 和 double 在存储的时候，存在精度损失的问题，很可能在值的比较时，得到不正确的结果。如果存储的数据范围超过 decimal 的范围，建议将数据拆成整数和小数分开存储。

10、没有特殊需求，所有字段都不允许为`null`。

11、指定表字符集为`utf8mb4`，不要用`utf8`，mysql中`utf8mb4`才是正宗的`UTF-8`编码。

12、保证所有表使用同样的排序规则。

> 排序规则`utf8mb4_unicode_ci`比`utf8mb4_general_ci`更精确，推荐都使用`utf8mb4_unicode_ci`，参考：<https://stackoverflow.com/questions/766809/whats-the-difference-between-utf8-general-ci-and-utf8-unicode-ci>

13、禁止使用外键、 一切外键概念在应用层解决 。

## 索引设计

1、 唯一索引名为` uk_字段名`；普通索引名则为 `idx_字段名` 。

2、根据业务的唯一特性，尽量给表加上唯一索引。

3、 在长度大于50的 `varchar `字段上建立索引时，必须指定索引长度，不然会对全部内容建立索引，浪费索引存储空间。

4、 建组合索引的时候，区分度最高的在最左边。

5、 索引需要建立在重复率低的字段上，重复率高的字段建立索引没有太大作用；。

##  **SQL语句**

1、 禁止使用存储过程和触发器，存储过程和触发器难以调试和扩展，更没有移植性。

2、 超过三个表禁止 join；需要 join 的字段，数据类型必须绝对一致；多表关联查询时，保证被关联的字段有索引。

3、除了`count`、`sum`、`max`、`min`等少数几个函数，将计算工作放在应用层来做，把计算和存储分离。

> 无状态应用迁移、扩容简单，有状态应用迁移、扩容复杂。



## 参考

* [sunshanpeng](https://gitee.com/sunshanpeng/blog/blob/master/MySQL%E5%BC%80%E5%8F%91%E8%A7%84%E8%8C%83.md)
* <https://github.com/jly8866/archer/blob/master/src/docs/mysql_db_design_guide.md>