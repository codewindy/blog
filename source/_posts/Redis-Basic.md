
---
title: Redis-Basic
date: 2016/3/1  14:48
tags: redis

---

 Redis 的数据结构
---

* redis 是以 key-value 的存储系统，其中 value 支持五种数据类型。
    * 字符串（String）
    * 哈希（hash）
    * 字符串列表（list）
    * 字符串集合（set）
    * 有序字符串集合（sorted set）

* 定义key的注意事项
    * key 最好不超过1024个字节，否则会降低查询效率同时消耗更多的内存。
    * key 要做到见名知意，在项目中最好统一规范。
    

<!-- more -->

---

字符串（String）
---

* 字符串是 Redis 中最基础的数据类型，它在 Redis 中是二进制安全的，这意味着 String 类型存入和取出的数据相同。在 Redis 中字符串的 value 最多可以容纳的数据长度是512M。

---
### **存储 (set)**
`set` `key` `value`

* key 不存在则直接存储 。
* key 存在则覆盖原值。
* 该操作总是响应 “OK”。

---
### **读取 (get)**
`get` `key`

* 如果 key 存在则返回 key 映射的 value 。
* key 映射的不是字符串类型则报错 。
* key 不存在则返回 `nil` 。

---
### **删除 (del)**
`del` `key`

* key 存在则删除 key，并返回受影响的数量。
* key 不存在则返回受影响的数量为 0 。

---
### **递增(incr) 递减(decr)**
`incr` `key`
`decr` `key`

* 该操作将 key 的 value 递增或递减1。前提是 value 能转换成整型。
* key 不存在则先存储 key 值为 0，再递增递减 。
* value 是整型则正常递增递减 。
* value 不是整型则报错。

---
### **指定增量（incrby）减量(decrby)**
`incrby` `key` `increment`
`decrby` `key` `decrement`

* 所有场景和响应与 `incr` 、`decr` 命令一致。**需要指定增量**。


---
### **追加（append）**
`append` `key` `value`

* 该操作一律将值作为字符串在末尾追加 `value`。
* key 存在，在末尾追加字符串。
* key 不存在，直接存储 value。
* 值是整数，将值作为字符串在末尾追加 `value`。

哈希（hash）
---

* Redis 中的 Hash 类型可以看成是具有 String key 和 String value 的 map 容器。该类型非常适合存储对象信息。例如用户的 username password 等。每一个 Hash 可以存储 4294967295 个键值对
### **存储（hset）**
`hset` `key` `field` `value`

* field 不存在，直接存储。
* field 存在，执行覆盖操作。


---
### **存储多个键值对（hmset）**
`hmset` `key` `field` `value` `field` `value` `...`

* field 不存在，直接存储。
* field 存在，执行覆盖操作。

---
### **读取（hget）**
`hget` `key` `field`

* field 不存在，正常读取 field 对应的值。
* field 存在，返回 `nil`。

---
### **读取多个值（hmget）**
`hmget` `key` `field` `field` `...`

* field 不存在，正常读取 field 对应的值。
* field 存在，返回 `nil` 。

---
### **读取全部（hgetall）**
`hgetall` `key`


---
### **删除（hdel）**
`hdel` `key` `field` `field` `...`

* 该命令可以同时删除多个字段，返回值是被删除字段的数量。
* field 存在，删除字段，返回被删除字段的数量。
* field 不存在， 返回0。

---
### **增量（hincrby）**
`hincrby` `key` `field` `numberValue`

* 为整型值增加指定的增量，可以指定增量为负数实现减量


---
### **判断字段是否存在（hexists）**
`hexists` `key` `field`

* field 存在，返回 1。
* field 不存在，返回 0。

---
### **获取指定 key 中字段个数（hlen）**
`hlen` `key`

---
### **获取指定 key 所有的字段（hkeys）**
`hkeys` `key`

---
### **获取指定 key 所有字段的值（hvals）**
`hvals` `key`

---


**列表（list）**
---

* 在 Redis 中，List 类型是按照插入顺序排序的字符串链表。和数据结构中的普通链表一样，我们可以在其头部（left）和尾部（right）添加新的元素。在插入时，如果该键并不存在，Redis 将为该键创建一个新的链表。如果链表中的所有元素都被移除，那么该键也将会被从数据库中移除。List 中可以包含的最大元素数量是 4294967295。

* 从元素插入和删除的效率角度来看，如果我们是在链表的两头插入或删除，这将是非常高效的操作，即使链表中已经存储了百万条记录，该操作也可以在常量时间内完成。如果插入或删除操作是作用于链表中间，那将会是非常低效的。

---
### **两端添加（lpush）（rpush）**
* 在指定的 key 映射的 list 头部或尾部插入一个或多个 value 。如果 key 不存在，该命令在插入之前先创建一个空链表，之后再插入数据。插入成功，返回插入的数量。
`lpush` `key` `value` `value` `...`
`rpush` `key` `value` `value` `...`

---
### **读取列表（lrange）**
`lrange` `key` `startIndex` `endIndex`

* 从链表中读取从 startIndex（包含） 到 endIndex（包含） 的元素。索引从 0 开始，endIndex 可以为负数，-1 表示尾部的元素，-2 表示倒数第二个元素，以此类推。

--- 
### **两端弹出（lpop）（rpop）**
`lpop` `key`
`rpop` `key`

* 从链表的头部或尾部删除元素，如果 key 存在，则返回被删除的元素，如果 key 不存在，则返回 nil

---
### **获取列表中元素的个数（llen）**
`llen` `key`

---
### **两端插入（仅当list存在）（lpushx）（rpushx）**
`lpushx` `key` `value`
`rpushx` `key` `value`

* 仅当 List 存在时，向列表两端插入**一个**元素，如果 List 不存在则不插入。

---
### **删除（lrem）**
`lrem` `key` `count` `value`

* 从 key 中删除 count 个值为 value 的元素。如果 count 大于 0，从头部开始查找 count 个值为 value 的元素。如果 count 小于 0，则从尾部开始遍历。如果 count 等于 0，则删除所有值等于 value 的元素。

---
### **修改元素（lset）**
`lset` `key` `index` `value`

* 设置链表中索引为 index 的元素值。0 代表头部元素，-1 代表尾部元素，索引不存在则报错。

---
### **插入元素（linsert）**
`linsert` `key` (`before` | `after`) `element` `value`

* 在第一个 element 元素之前或者之后插入值为 velue 的元素。

---
### **弹出/添加**
`rpoplpush` `resource` `destination`
...

* 从源链表的尾部弹出元素并添加到目标链表的头部，目标链表可以与源链表相同。

---
 集合（set）
---
 * 在 Redis 中，我们可以将 Set 类型看作没有排序的字符集合，和 List 类型一样，我们也可以在该类型的数据值上执行添加、删除或判断某一元素是否存在等操作。这些操作的时间复杂度为 O(1)，即常量时间内完成。Set 可包含的最大元素数量是 4294967295。
 * 和List集合不同的是，<font style="color:black; background:yellow">Set集合中不允许出现重复的元素</font>。相比 List 类型，Set 类型在功能上还存在着一些非常重要的特性，即在服务端完成多个 Set 之间的聚合计算操作，如 unions、intersections、defferences。由于这些操作均在服务端完成，因此效率极高，而且也节省了大量的网络 IO 开销。
 
---
### **存储（sadd）**
`sadd` `key` `value` `value` `...`

* 向 Set 中存储一个或多个值，如果要添加的值以及存在该 Set 中则不会重复添加。

---
### **删除（srem）**
`srem` `key` `value` `value` `...`

* 删除指定 key 中的一个或多个元素，如果值不存在不作任何操作。

---
### **读取（smembers）**
`smembers` `key`

* 获取该 key 中的所有元素。

---
### **判断元素是否存在（sismember）**
`sismember` `key` `element`

* 判断 element 是否存在于 key 中，存在返回 1，不存在返回 0，或者 key 不存在也返回 0。

---
### **差集运算（sdiff）**
`sdiff` `key1` `key2` `key3` `...`

* 返回 key1 与后面的 key 的差集成员。

---
### **交集运算（sinter）**
`sinter` `key1` `key2` `key3` `...`

* 返回所有 key 中的交集成员。

---
### **并集运算（sunion）**
`kunion` `key1` `key2` `key3` `...`

* 获得所有 key 中的并集成员。

---
### **获取元素数量（scard）**
`scard` `key`


---
### **随机获得元素（srandmember）**
`sranmember` `key`


---
### **存储差集（sdiffstore）、交集（sinterstore）和并集（sunionstore）**
`sdiffstore` `destination` `key1` `key2` `...`
`sinterstore` `destination` `key1` `key2` `...`
`sunionstore` `destination` `key1` `key2` `...`

* 将差集、交集和并集的运行结果存储到 destination 中。

---

 有序集合（sortedset）
---
* Sorted-Set 和 Set 类型极为相似，它们都是字符串的集合，都不允许重复的成员出现在一个 Set 中。它们之间的主要差别是 Sorted-Set 中的每一个成员都会有一个分数（score）与之关联，Redis 正是通过分数来为集合中的成员进行从小到达的排序。尽管 Sorted-Set 中的成员必须是唯一的，但是分数（score）却是可以重复的。

### **存储（zadd）**
`zadd` `key` `score1` `member1` `score2` `member2` `...`

* 添加成员及成员分数到 key 中，如果该成员已经存在，则更新该成员的分数。

---
### **获取分数（zscore）**
`zscore` `key` `member`

* 获得 member 的分数。

---
### **获取集合数量（zcard）**
`zcard` `key`


---
### **删除（zrem）**
`zrem` `key` `member1` `member2` `...`

* 删除一个或多个成员。

---
### **范围查询**
`zrange` `key` `start` `end` `[withscores]`

* 获取索引从 start（包含） 到 end（包含） 的成员，end 可以是负数，-1 表示最后一个，-2 表示倒数第二个，以此类推。
* [widthscores]指示返回的成员具有分数。

---
### **降序查询（zrevrange）**
`zrevrange` `key` `start` `end` `[withscore]`

* 查询 start 到 end 的成员并反转，实现降序查询。

---
### **按照排名范围删除（zremrangebyrank）**
`zremrangebyrank` `key` `start` `end`

* 删除集合中索引从 start（包含） 到 end（包含） 的元素。

---
### **按照分数删除（zremrangebyscore）**
`zremrangebyscore` `key` `minScore` `maxScore`

* 删除集合中分数从 minScore（包含） 到 maxScore（包含） 的元素。

---
### **根据分数范围查询（zrangebyscore）**
`zrangebyscore` `key` `minScore` `maxScore` `[withscore]` `[limit` `offset` `count]`

* 根据指定的分数范围查找成员，可以用 limit 指定显示记录的条数。

---
### **为指定成员增加分数（zincrby）**
`zincrby` `key` `increment` `member`

* 为指定的成员增加指定的分数，返回值是更新后的分数。

---
### **获取指定分数区间的成员数量（zcount）**
`zcount` `key` `minScore` `maxScore`

* 获得分数在 minScire（包含） 到 maxScore（包含） 的成员数量。

---
### **获取成员排名（zrank）（zrevrange）**
`zrank` `key` `member`
`zrevrank` `key` `member`

* 获取成员的升序或降序排名，排名从0开始计算。

---
Keys 的通用操作
---
### **查找 Key（keys）**
`keys` `pattern`

* 模糊查找 Key 。***** 表示任意一个或多个字符，**？**表示任意一个字符。

---
### **判断 Key 是否存在（exists）**
`exists` `key`

* 判断指定的 Key 是否存在，存在返回 1，不存在返回 0。

---
### **重命名 Key（rename）**
`rename` `key` `newkey`


---
### **设置 Key 的有效时间（expire）**
`expire` `key` `time`

* 为 Key 设置有效时间，过期后将从内存中清除。time 的单位为：秒。

---
### **获取 Key 的有效时间（ttl）**
`ttl` `key`

* 获取 Key 的有效时间，如果没有设置则返回 -1，如果 key 不存在则返回 -2。

---
### **获取 Key 的类型（type）**
`type` `key`

* 获取 Key 的类型，如果 Key 不存在返回None。
* [redis源码string分析](https://blog.csdn.net/qq_33996921/article/details/105226259)
* [redis String源码分析 debug object key1](https://artisan.blog.csdn.net/article/details/105757490)