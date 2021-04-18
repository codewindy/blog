---
title: JVM学习笔记
date: 2017/6/31  14:24
tags: jvm

---
# 内存分配
堆内存有以下三个主要区域
* 新生代
    * Eden空间，新创建的实例都通过Eden空间进入运行时内存区域；
    * Survivor 0空间，存活时间长的实例将会从Eden空间移动到 Survivor 0空间；
    * Survivor 1空间，存活时间更长的实例将会从Survivor空间移动到 Survivor 1空间；

<!-- more -->

* 老年代
    * 大对象直接进入老年代，比如特别长的字符串；
    * 虚拟机给每个对象定义了一个对象年龄计数器，如果对象在Eden空间出生并经过第一次MinorGC仍然存活，并且能够被Survivor容纳的话，将被移动到Survivor 0空间，同时对象的年龄设为1。对象在Survivor中没熬过一次MinorGC，年龄就增加1，到达一定程度（默认15岁）就被晋升到老年代中。

* 永久代
    * 包含类、方法等细节元信息。


![](https://github.com/codewindy/DesignPattern/blob/master/pic4hexo/4.jpg)
![](https://github.com/codewindy/DesignPattern/blob/master/pic4hexo/31.jpg)
![](https://github.com/codewindy/DesignPattern/blob/master/pic4hexo/47.jpg)


* 对什么进行回收
    * 枚举出所有的GC roots，从roots出发向下搜索，搜索走过的路称为引用链，在这些链上的对象在搜索的过程中会被打上标记，然后gc再做一次扫描，把没有被标记的对象全部清理掉,虚拟机为每个对象定义了一个年龄计数器，每次清理活下来的对象年龄往上递增，并移动到Survivor区域，当年龄达到15岁（默认的，可以修改）直接升到老年代。
    * 那些可以做roots引用点的是：
        * 栈中的引用对象
        * 方法区中静态引用指向的对象
        * 方法区中常量引用指向的对象

* 何时回收
    * Java GC主要针对Java堆区域进行垃圾回收，堆内存被分为新生代和老年代，其中新生代又分成Eden、Survivor0和Survivor1三块区域，新的对象都在Eden区创建出来。
    * 新生代的垃圾回收叫做minor gc，当Eden区没有足够的空间分配给新对象的时候就触发minor gc，当老年代剩余空间不足以放置从新生代升过来的对象时触发full gc
    * 新生代垃圾收集算法是复制算法
    * 老年代的是标记-清除-压缩算法

在新生代中，每次垃圾收集都发现有大批对象死去，只有少量存活，则使用复制算法，新生代内存被分为一个较大的Eden区和两个较小的Survivor区，每次只使用Eden区和一个Survivor区，当回收时将Eden区和Survivor还存活着的对象一次性的拷贝到另一个Survivor区上，最后清理掉Eden区和刚才使用过的Survivor区，Eden和Survivor的默认比例是8：1，可以使用-XX:SurvivorRatio来设置该比例。

而老年代中对象存活率高，没有额外的空间对它进行分配担保，必须使用“标记-清理”或“标记-整理”算法。

内存分配图

Java VisualVM 监控


# 垃圾回收算法
* <font color='red'>标记-清除算法</font>
   在程序运行期间，若可以使用的内存被耗尽，GC线程就会被触发并将程序暂停，随后扫描内存中所有的对象，将依旧存活的对象标记一遍，然后执行清除操作，清除没有被标记的对象，释放内存。（缺点：产生大量的内存碎片）
* <font color='red'>复制算法</font>
复制算法是为了解决标记-清除算法所产生的内存碎片。
首先将内存大小相等的两部分，每次只使用其中的一部分，等这部分用完了，就把活着的对象移动到另一边，然后把剩下的部分全部清除掉。
优点：避免产生内存碎片
缺点：造成浪费。本来挺大一片地方，现在只能用一半，搞得挺不爽的
* <font color='red'>标记-整理算法</font>
算法不直接对可回收对象进行清理，而是让所有可用的对象都向一端移动。然后直接清理掉边界意外的内存。
很显然，整理这一下需要时间，所以与标记清除算法相比，这一步花费了不少时间，但从长远来看，这一步还是很有必要的。
该算法可谓“道德高尚，自己栽树，后人乘凉”
* <font color='red'>分代收集</font>
根据对象存活周期的不同将内存划分为几块。一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。在新生代中，每次垃圾收集时都有大批对象死去，只有少量存活，那就使用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。老年代中存活率高，使用标记清理或者标记整理算法来回收。

# GC调优
| 配置 | 描述 |
| -- |
| -Xms | 初始堆内存大小 |
| -Xmx | 堆内存最大值（最大不要超过物理内存的80%） |
| -Xnm | 新生代大小 |
| -XX:permSize | 初始永久代大小 |
| -XX:MaxPermSize | 永久代最大容量 |

* 对新生代中的Eden区和survivor区以及老年代区域的大小进行调整。
* 大对象直接进入老年代
* 长期存活的对象直接进入老年代

# 参考
* [jvm Tuning pdf](http://ot2hu9qoc.bkt.clouddn.com/memorymanagement-whitepaper-150215.pdf)
* [jvms8.pdf](http://ot2hu9qoc.bkt.clouddn.com/jvms8.pdf)
* 深入理解jvm虚拟机
* [Giraffe](https://yemengying.com/)
* [五月的仓颉](https://www.cnblogs.com/xrq730/p/4989451.html)