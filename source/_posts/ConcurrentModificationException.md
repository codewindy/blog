
title: 集合类的并发修改异常分析
date: 2017-5-22 23:21
tags: Exception
---
===
<font style="color:black; background:yellow">List集合类中并发修改异常的处理</font>
> 在oracle 起别名如果有数字/或者空格特殊字符必须加""
> 主要读源码 String/ArrayList.iterator()/TreeMap/HashMap
> alt shift  h 生成equals 和hashcode 方法  想通过hashcode 找火车表的座位 ,然后如果hashcode 相同就有backet 桶结构再来比较equals如果相同就不存
> 31 是一个质数 不大不小在int 里面 且 2 的5 次方 -1  	 2<<4-1 100000-1  32  -->31

* hashcode() 在hashSet添加元素的时候会自动调用如果相同就调用equals 在比较为了提高效率,属性相同的对象返回值必须相同,属性不同的返回值尽量不同
* equals() 属性相同返回true 不存 属性不同返回false ,说明对象不同就存到一个bucket桶子里面 链表的方式存储

<!--more-->
* 书写顺序: select distinct..`*` from ..where[不能接聚合函数] ..group by ..having[聚合函数过滤条件] order by..limit[offset ,count/oracle里面没有]..desc
	sql语句的执行顺序: 嵌套子查询出来的 () 包起来 在重新起别名salavg,一张表自己内连接

	from emp where group by ..having ..select..rownum..order by..desc
	join on / is not null /group by 为毛线要在select 里面拼接条件


**TODO_List**
1. 重看HashMap 在jdk1.7和1.8 内部实现的区别
2. for循环删除需要i-- 因为list.remove() 一个元素会使得list向上移动所以需要改成i--
3. list.remove(i--),在list.iterator里面重写的方法实现了i-- ,你不-- 就会漏删除,所以可以直接用iterator.remove();不能用list.remove("bbb")否则并发修改异常;
4. foreach 里面用的就是iterator迭代器,所以不能list.remove("bbb");
```java
	* Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.ArrayList$Itr.checkForComodification(Unknown Source)
	at java.util.ArrayList$Itr.next(Unknown Source)
	at com.itheima.treeset.TestDeleteofArrayList.main(TestDeleteofArrayList.java:34)

	  public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                AbstractList.this.remove(lastRet);
                if (lastRet < cursor)
                    cursor--;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException e) {
                throw new ConcurrentModificationException();
            }
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
```


一 .并发修改异常的实例分析
---
1. FJ_day17_20总结讲解内容

```java

    package com.codewindy.treeset;

    import java.util.ArrayList;
    import java.util.Iterator;

    public class TestDeleteofArrayList {
    	public static void main(String[] args) {
    		ArrayList<String> list = new ArrayList<>();
    		list.add("aaa");
    	/*	list.add("bbb");
    		list.add("bbb");*/

    		list.add("bbb");
    		list.add("ccc");
    		list.add("bbb");
    		list.add("ddd");
    		list.add("eee");
    		//普通for 循环可以删除 但是i 需要--,删除元素 后list 会向上移动
    		//所以漏掉了一个
    	/*	for(int i=0;i<list.size();i++){
    			if(list.get(i).equals("bbb")){
    				list.remove(i--);*/

    //	--------------上面普通for 下面方法2 Iterator实现删除----------------------------------------------
    	/*			Iterator<String> iterator = list.iterator();
    				while(iterator.hasNext()){
    					String temp = iterator.next();
    					if(temp.equals("bbb")){
    						//list.remove("bbb");//不能用list自己的remove 方法并发修改异常
    						iterator.remove();
    					}
    				}
    				*/
    		for (String str : list) {
    			if(str.equals("bbb")){
    				list.remove(str);
    			}
    		}

    		System.out.println(list);
    }}

```
二. HashMap 在jdk1.7和1.8 内部实现的区别
---


三. count(1) 和count(*) 的区别
---

    ```sql

    sql syntax

    group by 一般和聚合函数一起使用才有意义,比如 count sum avg等,使用group by的两个要素:
       (1) 出现在select后面的字段 要么是是聚合函数中的,要么就是group by 中的.
       (2) 要筛选结果 可以先使用where 再用group by 或者先用group by 再用having
    group by后面的字段必须与select后面的没有使用聚合函数的字段匹配
    The GROUP BY syntax combines scalar column expressions and aggregate expressions. An asterisk (*) is allowed only in the COUNT(*) aggregate function.
    The SQL GROUP BY syntax

    The general syntax is:
    SELECT column-names
      FROM table-name
     WHERE condition
     GROUP BY column-names

    The general syntax with ORDER BY is:
    SELECT column-names
      FROM table-name
     WHERE condition
     GROUP BY column-names
     ORDER BY column-names


    SELECT 姓名
    FROM 学生表
    WHERE NOT EXISTS
    (
        SELECT *
        FROM 选课表
        WHERE 学生表.学号 = 选课表.学号 AND 课程号 = 'C1'
    );

     所以count(1)和count(主键)这两个效率高。
     不相关子查询：子查询的查询条件不依赖于父查询的称为不相关子查询。
    相关子查询：子查询的查询条件依赖于外层父查询的某个属性值的称为相关子查询，带EXISTS 的子查询就是相关子查询
    EXISTS表示存在量词：带有EXISTS的子查询不返回任何记录的数据，只返回逻辑值“True”或“False”

    Having与Where的区别

    where 子句的作用是在对查询结果进行分组前，将不符合where条件的行去掉，即在分组之前过滤数据，where条件中不能包含聚组函数，使用where条件过滤出特定的行。
    having 子句的作用是筛选满足条件的组，即在分组之后过滤数据，条件中经常包含聚组函数，使用having 条件过滤出特定的组，也可以使用多个分组标准进行分组。

    这就是需要注意的一点，在select指定的字段要么就要包含在Group By语句的后面，作为分组的依据；要么就要被包含在聚合函数中。

    ```

四 .下面这段话摘自Effective Java
---

    ```

    在程序执行期间，只要equals方法的比较操作用到的信息没有被修改，那么对这同一个对象调用多次，hashCode方法必须始终如一地返回同一个整数。
    如果两个对象根据equals方法比较是相等的，那么调用两个对象的hashCode方法必须返回相同的整数结果。
    如果两个对象根据equals方法比较是不等的，则hashCode方法不一定得返回不同的整数。
    　　对于第二条和第三条很好理解，但是第一条，很多时候就会忽略。在《Java编程思想》一书中的P495页也有同第一条类似的一段话：

    　　“设计hashCode()时最重要的因素就是：无论何时，对同一个对象调用 hashCode()都应该产生同样的值。如果在讲一个对象用put()添加进HashMap时产生一个hashCdoe值，而用get()取出时却产生 了另一个hashCode值，那么就无法获取该对象了。所以如果你的hashCode方法依赖于对象中易变的数据，用户就要当心了，因为此数据发生变化 时，hashCode()方法就会生成一个不同的散列码”。

    ```

总结两种比较器Comparable和Comparator，后者相比前者有如下优点：
---

1.  如果实现类没有实现Comparable接口，又想对两个类进行比较（或者实现类实现了Comparable接口，但是对compareTo方法内的比较算法不满意），那么可以实现Comparator接口，自定义一个比较器，写比较算法

2.  实现Comparable接口的方式比实现Comparator接口的耦合性要强一些，如果要修改比较算法，要修改Comparable接口的实现类，而实现Comparator的类是在外部进行比较的，不需要对实现类有任何修改。从这个角度说，其实有些不太好，尤其在我们将实现类的.class文件打成一个.jar文件提供给开发者使用的时候。实际上实现Comparator接口的方式后面会写到就是一种典型的策略模式。
