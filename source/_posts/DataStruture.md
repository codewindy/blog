
title: 常见的数据结构和算法(上)
date: 2017-5-25 08:00
tags: HashMap
---
<font style="color:black; background:yellow">TreeMap 源码里面有红黑树,要学会自我总结和归纳知识,反思避免错误</font>
<font style="color:black; background:yellow">**[红黑树BST的实现](https://www.cs.usfca.edu/~galles/visualization/RedBlack.html)**</font>
<font color="blue">**[具体图形化帮助理解请参考,旧金山大学usfca的算法演示](https://www.cs.usfca.edu/~galles/visualization/flash.html)**</font>
> 从简单的开始BubbleSort-->SelectSort-->BinarySort-->QuickSort-->DualPivotQuickSort
> HashMap(自己实现一个简单的)-->TreeMap-->Red_Black_Tree

一. 冒泡排序BubbleSort[2种方式实现]
---


<!--more-->


```java
/**
 * FJ day13 javaSE常见对象 数组冒泡排序
 *
 * @author yui
 *
 */
package com.codewindy.sorting;
public class Demo1_BubbleSort {
     public static void main(String[] args) {
          int[] arr = { 66, 11, 22, 33, 1, 55 };
          bubbleSort(arr);
          System.out.println(arr2String(arr));
     }
     /**
      * 打印 到五角星 冒泡排序 返回值类型void 参数列表 int[] arr
      * 1. void
      * 2. int[] arr
      *
      */




     public static void selectSort(int[] arr){//arr.length=6
          for (int i = 0; i < arr.length-1; i++) {//-1 是应为6个数只需要比较5次

              for (int j = 0; j < arr.length-1-i; j++) {//-1 防止索引越界 -i是为了提高效率

                   if(arr[j]>arr[j+1]){
                   /*   int temp=arr[i];
                        arr[i]=arr[j];
                        arr[j]=temp;*/
                        Demo1_BubbleSort.swap(arr,j, j+1);
                        // 调用静态的换位方法
                   }
              }
          }
     }
     /*------------------------------上面的是第一种方式实现BubbleSort,下面的是第二种有图有真相-----------*/
     public static void bubbleSort(int[] arr) {
          // 定义参数
          boolean swapped = true;
          int j = 0;
          int temp;
          while (swapped) {
              swapped = false;
              j++;
              for (int i = 0; i < arr.length - j; i++) {
                   // 对i ,i+1 进行交换
                   if (arr[i] > arr[i + 1]) {
                        temp = arr[i];
                        arr[i] = arr[i + 1];
                        arr[i + 1] = temp;
                        swapped = true; // { 66, 11, 22, 33, 1, 55 }进入这个方法 说明还能继续冒泡排序
                   }
              }
          }
     }
     /**
      * 打印数组 转化车位那个字符串的方法 1. 返回值的类型string 2. 参数列表 int[] arr
      *
      */
     public static String arr2String(int[] arr) {
          StringBuilder sb = new StringBuilder();
          sb.append("[");
          for (int i = 0; i < arr.length; i++) {
              if (i == arr.length - 1) {
                   sb.append(arr[i]).append("]");
              } else {
                   sb.append(arr[i]).append(", ");
              }
          }
          return sb.toString();
     }

     /**
      * 交换数组的位置 1. 返回值类型void 2. 参数列表int[] arr,int i,int j
      */
     public static void swap(int[] arr, int i, int j) {
          int temp = arr[i];
          arr[i] = arr[j];
          arr[j] = temp; // 默认左边的> 右边的 冒泡排序向后面移动
     }
}

```

二. 选择排序的简单实现
---

  ```java


  package com.codewindy.sorting;
  public class Demo2_selectSort {
       public static void main(String[] args){

            int[] arr={66,55,44,33,22,11};
            selectSort(arr);
            String arr2String = Demo1_BubbleSort.arr2String(arr);
            System.out.println("选择排序打印输出结果:" +arr2String);
       }

       /**
        * 选择排序
        * 1. void
        * 2. int[] arr
        *
        */
       public static void selectSort(int[] arr){//arr.length=6
            for (int i = 0; i < arr.length-1; i++) {

                for (int j = i+1; j < arr.length; j++) {

                     if(arr[i]>arr[j]){
                     /*   int temp=arr[i];
                          arr[i]=arr[j];
                          arr[j]=temp;*/
                          Demo1_BubbleSort.swap(arr, i, j);
                          // 调用静态的换位方法
                     }
                }
            }
       }
  }




  ```

三 .BinarySearch[Arrays参考源码]二分查找法
---

  ```java

  package com.codewindy.sorting;
  public class Demo3_BinarySearch {
       public static void main(String[] args) {
            int[] arr = { 11, 22, 33, 44, 55, 66, 77 };
            // System.out.println(arr.length); //打印数组元素的个数 也是长度 角标 从0 开始
            // System.out.println(arr[arr.length]); //数组角标越界异常
            // System.out.println(binarySearch(arr, 22));
            // System.out.println(binarySearch(arr, 66));
            // System.out.println(binarySearch(arr, 888));
            System.out.println("--------------通过jdk方式实现--------------------");
            System.out.println(binarySearch1(arr, 22));
            System.out.println(binarySearch1(arr, 66));
            System.out.println(binarySearch1(arr, 888));
       }
       /*------------第二种方式--------------------*/
       public static int binarySearch1(int[] a, int key) {
            return binarySearch0(a, 0, a.length, key);
       }
       private static int binarySearch0(int[] a, int fromIndex, int toIndex, int key) {
            int low = fromIndex;
            int high = toIndex - 1;
            while (low <= high) {
                int mid = (low + high) >>> 1;
                int midVal = a[mid];
                if (midVal < key)
                     low = mid + 1;
                else if (midVal > key)
                     high = mid - 1;
                else
                     return mid; // key found
            }
            return -(low + 1); // key not found.
       }
       /**
        * 二分查找法
        *
        * @param arr
        *            数组,int key 形参
        * @return 返回值是int -1 表示找不到
        */
       public static int binarySearch(int[] arr, int key) {
            // 记录最小索引的值
            int min = 0;
            // 记录最大索引的值
            int max = arr.length - 1;
            // 求出中间的位置索引的值(min+max)/2
            int mid = (max + min) >>> 1;
            // 判断
            while (arr[mid] != key) {
                if (arr[mid] > key) {
                     max = mid - 1;
                } else if (key > arr[mid]) { // 如果要找的key >mid 中间值
                     min = mid + 1; // 那么中间值
                }
                if (min > max) {
                     return -1;
                }
                mid = (max + min) >>> 1; // 无论最大值 还是最小值改变中间值需要改变
            }
            return mid;
       }
  }

  ```
四 .快速排序是对冒泡排序的优化
---

  ```java
  思想是: 先取数组的pivot left /right 然后进行刷选,数组中 <pivot 的值往左边丢, > pivot 的数组的值往右边丢,最终递归实现排序nlog2N 的时间复杂度和
  二叉树的效率基本类似



  package com.codewindy.sorting;
  import java.util.Arrays;
  public class Demo08_MyquickSort {
       public static void main(String[] args) {
            int[] arr={7,5,3,2,5,8,98,3};
            quicksort(arr, 0, arr.length-1);
            System.out.println(arr2String(arr));
       }

       /**
        * 定义一个方法将 数组转化成字符串
        * @param arr
        * @return
        */
       public static String arr2String(int[] arr){
            if(arr==null) return "null";
            int iMax=arr.length-1;
            if(iMax==-1) return "[]";

            StringBuilder sb = new StringBuilder();
            sb.append("[");
            for (int i = 0; ; i++) {
                sb.append(arr[i]);
                if(i==iMax){
                     return sb.append("]").toString(); //在数组iMax 位置处时候返回
                }
                sb.append(", ");
            }
       }
       /**
        * 将数组中的元素进行交换swap
        * @param arr
        * @param l
        * @param r
        */
       public static void swap(int[] arr,int l, int r){
            int temp=arr[l];
            arr[l]=arr[r];
            arr[r]=temp;

       }


       /**
        * 快速排序 是 对BubbleSort 的一种算法优化,具体实现如下
        * @param arr  要进行排序的数组
        * @param left  数组的起始index 位置 一般0
        * @param right 数组的iMax length 的值arr.lenth-1
        */
       public static void quicksort(int[] arr,int left,int right){

            int l=left;
            int r=right;

            int pivot=arr[(l+r)>>1]; //找中间位置
            while(l<r){
                while(arr[l]<pivot) l++;
                while(pivot<arr[r]) r--;

                //出口 如果发生交叉越界就跳出,跳出while 循环
                if(l>=r) break;

                /*int temp=arr[l];
                arr[l]=arr[r];
                arr[r]=temp;*/
                swap(arr, l, r);

                /**
                 * 这里写的看不懂了,应该是左边-->向右遍历到了pivot 临界值,就让r 继续向左移动
                 */
                if(arr[l]==pivot) --r;
                if(arr[r]==pivot) ++l;
            }
            //当 左边和右边相等的时候,说明已经partition 分段的临界点,需要越界一位
            if(l==r){
                l++;
                r--;

            }
            /**
             * 但第一次partition 将数组分段结束后  对左右2边的数组进行递归处理
             * 难点: 递归
             * continue 和break 的区别
             * return finally 的执行顺序?
             */
            if(left<r) quicksort(arr, left, r);
            if(l<right) quicksort(arr,l,right);

       }

  }

    /**
     * 写一个固定容量同步容器，拥有put和get方法，以及getCount方法，
     * 能够支持2个生产者线程以及10个消费者线程的阻塞调用
     *
     * 使用wait和notify/notifyAll来实现
     *
     * 使用Lock和Condition来实现
     * 对比两种方式，Condition的方式可以更加精确的指定哪些线程被唤醒
     *
     * @author codewindy
     */
    package jp.codewindy.producer.consumer;

    import java.util.LinkedList;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.locks.Condition;
    import java.util.concurrent.locks.Lock;
    import java.util.concurrent.locks.ReentrantLock;

    public class MyContainer<T> {
        final private LinkedList<T> lists = new LinkedList<>();
        final private int MAX = 10; //最多10个元素
        private int count = 0;

        private Lock lock = new ReentrantLock();
        private Condition producer = lock.newCondition();
        private Condition consumer = lock.newCondition();

        public void put(T t) {
            try {
                lock.lock();
                //想想为什么用while而不是用if？
                while(lists.size() == MAX) {
                    producer.await();
                }

                lists.add(t);
                ++count;
                //通知消费者线程进行消费
                consumer.signalAll();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }

        public T get() {
            T t = null;
            try {
                lock.lock();
                while(lists.size() == 0) {
                    consumer.await();
                }
                t = lists.removeFirst();
                count --;
                //通知生产者进行生产
                producer.signalAll();

            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
            return t;
        }

        public static void main(String[] args) {
            MyContainer<String> c = new MyContainer<>();
            //启动消费者线程
            for(int i=0; i<10; i++) {
                new Thread(()->{
                    for(int j=0; j<5; j++) System.out.println(c.get());
                }, "c" + i).start();
            }

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            //启动生产者线程
            for(int i=0; i<2; i++) {
                new Thread(()->{
                    for(int j=0; j<25; j++) c.put(Thread.currentThread().getName() + " " + j);
                }, "p" + i).start();
            }
        }
    }

  ```