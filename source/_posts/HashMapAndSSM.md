---
title: HashMap
date: 2017/3/20 23:25
tags: hashmap

---

HashMap的JDK 源码注释
---
1. 基于Map接口实现、允许null键/值、非同步、不保证有序(比如插入的顺序)、也不保证序不随时间变化。
2. Capacity就是bucket的大小，Load factor就是bucket填满程度的最大比例。如果对迭代性能要求很高的话不要把capacity设置过大，也不要把load factor设置过小。当bucket中的entries的数目大于capacity*load factor时就需要调整bucket的大小为当前的2倍。

<!-- more -->
3. put函数的实现

		对key的hashCode()做hash，然后再计算index;
		如果没碰撞直接放到bucket里；
		如果碰撞了，以链表的形式存在buckets后；
		如果碰撞导致链表过长(大于等于TREEIFY_THRESHOLD)，就把链表转换成红黑树；
		如果节点已经存在就替换old value(保证key的唯一性)
		如果bucket满了(超过load factor*current capacity)，就要resize。
4. get函数的实现

		bucket里的第一个节点，直接命中；
		如果有冲突，则通过key.equals(k)去查找对应的entry
		若为树，则在树中通过key.equals(k)查找，O(logn)；
		若为链表，则在链表中通过key.equals(k)查找，O(n)。

5. Hashmap的原理

	```
		通过hash的方法，通过put和get存储和获取对象。存储对象时，我们将K/V传给put方法时，它调用hashCode计算hash从而得到bucket位置，进一步存储，HashMap会根据当前bucket的占用情况自动调整容量(超过Load Facotr则resize为原来的2倍)。获取对象时，我们将K传给get，它调用hashCode计算hash从而得到bucket位置，并进一步调用equals()方法确定键值对。如果发生碰撞的时候，Hashmap通过链表将产生碰撞冲突的元素组织起来，在Java 8中，如果一个bucket中碰撞冲突的元素超过某个限制(默认是8)，则使用红黑树来替换链表，从而提高速度。
    ```

* 什么时候会使用HashMap？他有什么特点？
* 你知道HashMap的工作原理吗？
* 你知道get和put的原理吗？
* equals()和hashCode()的都有什么作用？
* 你知道hash的实现吗？为什么要这样实现？
* 如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？


HashMap 的总结归纳
---
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-11/46599362.jpg)

集合框架schema
---
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-20/23337435.jpg)
SpringMVC的处理流程
---
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-20/66747192.jpg)

SpringMVC 的处理流程和ssm整合
---
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-11/81747139.jpg)
struts2 的流程图
---
![](http://ot2hu9qoc.bkt.clouddn.com/17-8-20/29168157.jpg)