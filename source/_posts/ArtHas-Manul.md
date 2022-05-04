---
title: Arthas 排查问题
date: 2022-02-12 15:22:58
tags: arthas
---


# Arthas jad命令介绍

>  https://alibaba.github.io/arthas/jad.html

**将 JVM 中实际运行的 class 的 byte code 反编译成 java 代码 **

## 使用方法

命令格式：

```bash
jad class
```

示例：

```java
[arthas@30]$ jad java.lang.String

ClassLoader:

Location:


/*
 * Decompiled with CFR.
 */
package java.lang;

import java.io.ObjectStreamField;
import java.io.Serializable;
import java.io.UnsupportedEncodingException;
import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Comparator;
import java.util.Formatter;
import java.util.List;
import java.util.Locale;
import java.util.Objects;
import java.util.StringJoiner;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public final class String
implements Serializable,
Comparable<String>,
CharSequence {
    private final char[] value;
    private int hash;
    private static final long serialVersionUID = -6849794470754667710L;
    private static final ObjectStreamField[] serialPersistentFields = new ObjectStreamField[0];
    public static final Comparator<String> CASE_INSENSITIVE_ORDER = new CaseInsensitiveComparator();

    public String(byte[] bytes, int offset, int length) {
        String.checkBounds(bytes, offset, length);
        this.value = StringCoding.decode(bytes, offset, length);
    }

    public String(byte[] bytes, Charset charset) {
        this(bytes, 0, bytes.length, charset);
    }
...
```


# Arthas ognl命令介绍

>  https://alibaba.github.io/arthas/ognl.html

## 使用方法

命令格式：

```bash
ognl 'express'
```

### 调用静态函数

```bash
$ ognl '@System@getProperty("java.home")'
@String[/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64/jre]
```

###  执行多行表达式，赋值给临时变量，返回一个List

```bash
$ ognl '#value1=@System@getProperty("java.home"), #value2=@System@getProperty("java.runtime.name"), {#value1, #value2}'
@ArrayList[
    @String[/opt/java/8.0.181-zulu/jre],
    @String[OpenJDK Runtime Environment],
]
```
# Arthas thread命令介绍

>  https://alibaba.github.io/arthas/thread.html

查看当前Java进程的线程信息和线程的堆栈信息。

## 使用方法

命令格式：

```bash
thread [id] [-n 5]
```

示例：

### 显示所有线程的信息

```bash
$ thread
Threads Total: 16, NEW: 0, RUNNABLE: 7, BLOCKED: 0, WAITING: 5, TIMED_WAITING: 4, TERMINATED: 0
ID         NAME                             GROUP                 PRIORITY   STATE      %CPU       TIME       INTERRUPTE DAEMON
30         as-command-execute-daemon        system                9          RUNNABLE   72         0:0        false      true
23         as-session-expire-daemon         system                9          TIMED_WAIT 27         0:0        false      true
22         Attach Listener                  system                9          RUNNABLE   0          0:0        false      true
11         pool-2-thread-1                  main                  5          TIMED_WAIT 0          0:0        false      false
12         Thread-2                         main                  5          RUNNABLE   0          0:0        false      true
13         pool-3-thread-1                  main                  5          TIMED_WAIT 0          0:0        false      false
25         as-selector-daemon               system                9          RUNNABLE   0          0:0        false      true
14         Thread-3                         main                  5          TIMED_WAIT 0          0:0        false      false
26         pool-5-thread-1                  system                5          WAITING    0          0:0        false      false
15         Thread-4                         main                  5          RUNNABLE   0          0:0        false      false
1          main                             main                  5          WAITING    0          0:2        false      false
2          Reference Handler                system                10         WAITING    0          0:0        false      true
3          Finalizer                        system                8          WAITING    0          0:0        false      true
4          Signal Dispatcher                system                9          RUNNABLE   0          0:0        false      true
20         NonBlockingInputStreamThread     main                  5          WAITING    0          0:0        false      true
21         Thread-8                         main                  5          RUNNABLE   0          0:0        false      true

```

###  显示指定线程的运行堆栈

```bash
$ thread 1
"main" Id=1 WAITING on java.util.concurrent.CountDownLatch$Sync@29fafb28
    at sun.misc.Unsafe.park(Native Method)
    -  waiting on java.util.concurrent.CountDownLatch$Sync@29fafb28
    at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:836)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.doAcquireSharedInterruptibly(AbstractQueuedSynchronizer.java:997)
    at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireSharedInterruptibly(AbstractQueuedSynchronizer.java:1304)
    at java.util.concurrent.CountDownLatch.await(CountDownLatch.java:231)
```

### 显示当前最忙的前N个线程并打印堆栈

```bash
thread -n 3
"as-command-execute-daemon" Id=29 cpuUsage=75% RUNNABLE
    at sun.management.ThreadImpl.dumpThreads0(Native Method)
    at sun.management.ThreadImpl.getThreadInfo(ThreadImpl.java:440)
    at com.taobao.arthas.core.command.monitor200.ThreadCommand$1.action(ThreadCommand.java:58)
    at com.taobao.arthas.core.command.handler.AbstractCommandHandler.execute(AbstractCommandHandler.java:238)
    at com.taobao.arthas.core.command.handler.DefaultCommandHandler.handleCommand(DefaultCommandHandler.java:67)
    at com.taobao.arthas.core.server.ArthasServer$4.run(ArthasServer.java:276)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
    at java.lang.Thread.run(Thread.java:745)
 
    Number of locked synchronizers = 1
    - java.util.concurrent.ThreadPoolExecutor$Worker@6cd0b6f8
 
"as-session-expire-daemon" Id=25 cpuUsage=24% TIMED_WAITING
    at java.lang.Thread.sleep(Native Method)
    at com.taobao.arthas.core.server.DefaultSessionManager$2.run(DefaultSessionManager.java:85)
 
"Reference Handler" Id=2 cpuUsage=0% WAITING on java.lang.ref.Reference$Lock@69ba0f27
    at java.lang.Object.wait(Native Method)
    -  waiting on java.lang.ref.Reference$Lock@69ba0f27
    at java.lang.Object.wait(Object.java:503)
    at java.lang.ref.Reference$ReferenceHandler.run(Reference.java:133)
```


# Arthas trace命令介绍

>  <https://alibaba.github.io/arthas/trace.html>

**打印方法内部调用路径，并输出方法路径上的每个节点上耗时。**

*trace命令只会trace匹配到的函数里的子调用，并不会向下trace多层。因为trace是代价比较贵的，多层trace可能会导致最终要trace的类和函数非常多。*

## 使用方法

命令格式：

```bash
trace class method
```

示例：

```bash
[arthas@30]$ trace com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl checkPrePub
Press Q or Ctrl+C to abort.
Affect(class-cnt:2 , method-cnt:4) cost in 408 ms.
`---ts=2019-11-12 14:47:28;thread_name=http-nio-8266-exec-147;id=7938;is_daemon=true;priority=5;TCCL=org.springframework.boot.context.embedded.tomcat.TomcatEmbeddedWebappClassLoader@6c902fd5
    `---[3771.437071ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:checkPrePub()
        +---[0.003039ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getClusterCode() #157
        +---[0.001022ms] java.util.Map:get() #157
        +---[0.002185ms] com.sunshanpeng.platform.pub.common.enums.PubStatusEnum:getCode() #161
        +---[0.001843ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setStatus() #161
        +---[1645.580557ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:selectBySelective() #162
        +---[0.002757ms] org.springframework.util.CollectionUtils:isEmpty() #163
        +---[0.001587ms] com.sunshanpeng.platform.pub.common.enums.PubStatusEnum:getCode() #169
        +---[0.002123ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setStatus() #169
        +---[1478.93462ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:selectBySelective() #170
        +---[0.002329ms] org.springframework.util.CollectionUtils:isEmpty() #171
        `---[646.735412ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:buildPublishDetailDTO() #178
            `---[646.539364ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:buildPublishDetailDTO()
                +---[0.011378ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getPubEnv() #513
                +---[0.002328ms] org.springframework.util.StringUtils:isEmpty() #513
                +---[0.001789ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getClusterCode() #516
                +---[0.001076ms] org.springframework.util.StringUtils:isEmpty() #516
                +---[0.005232ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getSysCode() #519
                +---[0.001092ms] org.springframework.util.StringUtils:isEmpty() #519
                +---[0.004531ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getSysName() #522
                +---[9.95E-4ms] org.springframework.util.StringUtils:isEmpty() #522
                +---[0.001071ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getPubEnv() #525
                +---[0.001343ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getClusterCode() #525
                +---[0.001108ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getSysCode() #525
                +---[1.617415ms] com.sunshanpeng.platform.pub.service.EnvBindService:getRelationByArgs() #525
                +---[0.002319ms] java.util.List:get() #526
                +---[0.014244ms] java.util.List:size() #527
                +---[0.011744ms] com.sunshanpeng.platform.pub.dto.envbind.EnvBindDetailDTO:getJobName() #527
                +---[0.001469ms] org.springframework.util.StringUtils:isEmpty() #527
                +---[0.001367ms] com.sunshanpeng.platform.pub.dto.envbind.EnvBindDetailDTO:getJobName() #532
                +---[0.006322ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setJobName() #532
                +---[0.001252ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getPubEnv() #535
                +---[0.001285ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getClusterCode() #535
                +---[0.001253ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getSysCode() #535
                +---[643.723767ms] com.sunshanpeng.platform.pub.service.JenkinsService:getBuildArgs() #535
                +---[0.010914ms] com.sunshanpeng.platform.pub.common.enums.CommonStatusEnum:getCode() #536
                +---[0.006734ms] com.sunshanpeng.platform.pub.dto.envbind.EnvBindDetailDTO:getSwEnable() #536
                +---[0.014911ms] java.lang.Integer:equals() #536
                +---[0.002085ms] java.lang.Boolean:valueOf() #536
                +---[0.011168ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setApmEnable() #536
                +---[0.004956ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getPubParamKey() #542
                +---[0.002016ms] org.springframework.util.StringUtils:isEmpty() #542
                +---[0.002196ms] java.util.Map:get() #550
                +---[0.004316ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getPubParamValue() #551
                +---[0.001027ms] org.springframework.util.StringUtils:isEmpty() #551
                +---[0.004536ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getRemark() #559
                +---[0.001113ms] org.springframework.util.StringUtils:isEmpty() #559
                +---[0.004637ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setRemark() #560
                +---[0.001502ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getSysCode() #562
                +---[0.322113ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:getCiCode() #562
                +---[0.005395ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setCiCode() #562
                +---[0.004249ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setConsoleLog() #563
                +---[0.001335ms] java.lang.Integer:valueOf() #564
                +---[0.004697ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setBuildNo() #564
                +---[0.004276ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setCreator() #565
                +---[0.004354ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setCreatorCode() #566
                +---[0.020689ms] java.time.LocalDateTime:now() #567
                +---[0.004397ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setGmtCreate() #567
                +---[0.002075ms] java.time.LocalDateTime:now() #568
                +---[0.004739ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setGmtModify() #568
                +---[0.001317ms] com.sunshanpeng.platform.pub.common.enums.PubStatusEnum:getCode() #569
                `---[0.001365ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setStatus() #569
```

**可以看到目标方法的耗时为`3771.437071ms`，其中`com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:selectBySelective()`方法调用了两次，并且耗时都在1.5秒左右。**


## 优化前执行计划

```bash
mysql> explain select id, ci_code, build_no, creator_code, sys_code, sys_name, pub_env,cluster_code, job_name, pub_param_key, pub_param_value, status, remark,  creator, gmt_create, gmt_modify
from t_pub_publish 
where sys_code = "aim" and pub_env = "qa" and cluster_code = 'cn-hd-idc-test-1' and status = 1;
+----+-------------+---------------+------+---------------+------+---------+------+-------+-------------+
| id | select_type | table         | type | possible_keys | key  | key_len | ref  | rows  | Extra       |
+----+-------------+---------------+------+---------------+------+---------+------+-------+-------------+
|  1 | SIMPLE      | t_pub_publish | ALL  | NULL          | NULL | NULL    | NULL | 30544 | Using where |
+----+-------------+---------------+------+---------------+------+---------+------+-------+-------------+
1 row in set
```

## 加组合索引

```bash
ALTER TABLE `t_pub_publish`
ADD INDEX `idx_code` (`sys_code`, `pub_env`, `cluster_code`, `status`) ;
```

## 优化后执行计划

```bash
mysql> explain select id, ci_code, build_no, creator_code, sys_code, sys_name, pub_env,cluster_code, job_name, pub_param_key, pub_param_value, status, remark,  creator, gmt_create, gmt_modify
from t_pub_publish 
where sys_code = "aim" and pub_env = "qa" and cluster_code = 'cn-hd-idc-test-1' and status = 1;
+----+-------------+---------------+------+---------------+----------+---------+-------------------------+------+-----------------------+
| id | select_type | table         | type | possible_keys | key      | key_len | ref                     | rows | Extra                 |
+----+-------------+---------------+------+---------------+----------+---------+-------------------------+------+-----------------------+
|  1 | SIMPLE      | t_pub_publish | ref  | idx_code      | idx_code | 249     | const,const,const,const |    1 | Using index condition |
+----+-------------+---------------+------+---------------+----------+---------+-------------------------+------+-----------------------+
1 row in set
```

## 优化后耗时

```bash
[arthas@30]$ trace com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl checkPrePub 
Press Q or Ctrl+C to abort.
Affect(class-cnt:2 , method-cnt:1) cost in 390 ms.
`---ts=2019-11-13 13:42:15;thread_name=http-nio-8266-exec-143;id=7934;is_daemon=true;priority=5;TCCL=org.springframework.boot.context.embedded.tomcat.TomcatEmbeddedWebappClassLoader@6c902fd5
    `---[203.648108ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:checkPrePub()
        +---[min=4.7E-4ms,max=0.010168ms,total=0.010638ms,count=2] java.lang.Integer:<init>() #157
        +---[4.61E-4ms] java.lang.reflect.Method:invoke() #157
        +---[0.003624ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:getClusterCode() #157
        +---[0.001096ms] java.util.Map:get() #157
        +---[0.002196ms] com.sunshanpeng.platform.pub.common.enums.PubStatusEnum:getCode() #161
        +---[0.001674ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setStatus() #161
        +---[0.578989ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:selectBySelective() #162
        +---[6.84E-4ms] org.springframework.util.CollectionUtils:isEmpty() #163
        +---[5.82E-4ms] com.sunshanpeng.platform.pub.common.enums.PubStatusEnum:getCode() #169
        +---[5.45E-4ms] com.sunshanpeng.platform.pub.dto.publish.PublishDetailDTO:setStatus() #169
        +---[0.407368ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:selectBySelective() #170
        +---[5.59E-4ms] org.springframework.util.CollectionUtils:isEmpty() #171
        `---[202.38997ms] com.sunshanpeng.platform.pub.service.impl.PublishServiceImpl:buildPublishDetailDTO() #178
```

**优化后SQL查询时间降到1ms不到，总耗时200ms，效果明显。**



# Arthas watch命令介绍

> <https://alibaba.github.io/arthas/watch.html>

**方便的观察到指定方法的调用情况。**

能观察到的范围为：`入参`、`返回值`、`抛出异常`和`当前对象的属性`，通过编写 OGNL 表达式进行对应变量的查看。

## 使用方法

命令格式：

```bash
watch class method "{params,returnObj,throwExp}" -x 2
```

### 观察方法出参和返回值

```bash
$ watch demo.MathGame primeFactors "{params,returnObj}" -x 2
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 44 ms.
ts=2018-12-03 19:16:51; [cost=1.280502ms] result=@ArrayList[
    @Object[][
        @Integer[535629513],
    ],
    @ArrayList[
        @Integer[3],
        @Integer[19],
        @Integer[191],
        @Integer[49199],
    ],
]
```

### 调整`-x`的值，观察具体的方法参数值

```bash
$ watch demo.MathGame primeFactors "{params,target}" -x 3
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 58 ms.
ts=2018-12-03 19:34:19; [cost=0.587833ms] result=@ArrayList[
    @Object[][
        @Integer[47816758],
    ],
    @MathGame[
        random=@Random[
            serialVersionUID=@Long[3905348978240129619],
            seed=@AtomicLong[3133719055989],
            multiplier=@Long[25214903917],
            addend=@Long[11],
            mask=@Long[281474976710655],
            DOUBLE_UNIT=@Double[1.1102230246251565E-16],
            BadBound=@String[bound must be positive],
            BadRange=@String[bound must be greater than origin],
            BadSize=@String[size must be non-negative],
            seedUniquifier=@AtomicLong[-3282039941672302964],
            nextNextGaussian=@Double[0.0],
            haveNextNextGaussian=@Boolean[false],
            serialPersistentFields=@ObjectStreamField[][isEmpty=false;size=3],
            unsafe=@Unsafe[sun.misc.Unsafe@2eaa1027],
            seedOffset=@Long[24],
        ],
        illegalArgumentCount=@Integer[13159],
    ],
]
```

`-x`表示遍历深度，可以调整来打印具体的参数和结果内容，默认值是1。

### 条件表达式的例子

```bash
$ watch demo.MathGame primeFactors "{params[0],target}" "params[0]<0"
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 68 ms.
ts=2018-12-03 19:36:04; [cost=0.530255ms] result=@ArrayList[
    @Integer[-18178089],
    @MathGame[demo.MathGame@41cf53f9],
]
```

### 按照耗时进行过滤

```bash
$ watch demo.MathGame primeFactors '{params, returnObj}' '#cost>200' -x 2
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 66 ms.
ts=2018-12-03 19:40:28; [cost=2112.168897ms] result=@ArrayList[
    @Object[][
        @Integer[2141897465],
    ],
    @ArrayList[
        @Integer[5],
        @Integer[428379493],
    ],
]
```

`#cost>200`(单位是`ms`)表示只有当耗时大于200ms时才会输出，过滤掉执行时间小于200ms的调用

### 观察当前对象中的属性

```bash
$ watch demo.MathGame primeFactors 'target'
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 52 ms.
ts=2018-12-03 19:41:52; [cost=0.477882ms] result=@MathGame[
    random=@Random[java.util.Random@522b408a],
    illegalArgumentCount=@Integer[13355],
]
```

# Arthas tt命令介绍

>  https://alibaba.github.io/arthas/tt.html

**方法执行数据的时空隧道，记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测**

## 使用方法

命令格式：

```bash
tt -t class method
```

### 记录指定方法的每次调用环境现场

```bash
$ tt -t demo.MathGame primeFactors
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 66 ms.
 INDEX   TIMESTAMP            COST(ms)  IS-RET  IS-EXP   OBJECT         CLASS                          METHOD
-------------------------------------------------------------------------------------------------------------------------------------
 1000    2018-12-04 11:15:38  1.096236  false   true     0x4b67cf4d     MathGame                       primeFactors
 1001    2018-12-04 11:15:39  0.191848  false   true     0x4b67cf4d     MathGame                       primeFactors
 1002    2018-12-04 11:15:40  0.069523  false   true     0x4b67cf4d     MathGame                       primeFactors
 1003    2018-12-04 11:15:41  0.186073  false   true     0x4b67cf4d     MathGame                       primeFactors
 1004    2018-12-04 11:15:42  17.76437  true    false    0x4b67cf4d     MathGame                       primeFactors

```

### 检索调用记录

#### 列出所有调用记录

```bash
$ tt -l
 INDEX   TIMESTAMP            COST(ms)  IS-RET  IS-EXP   OBJECT         CLASS                          METHOD
-------------------------------------------------------------------------------------------------------------------------------------
 1000    2018-12-04 11:15:38  1.096236  false   true     0x4b67cf4d     MathGame                       primeFactors
 1001    2018-12-04 11:15:39  0.191848  false   true     0x4b67cf4d     MathGame                       primeFactors
 1002    2018-12-04 11:15:40  0.069523  false   true     0x4b67cf4d     MathGame                       primeFactors
 1003    2018-12-04 11:15:41  0.186073  false   true     0x4b67cf4d     MathGame                       primeFactors
 1004    2018-12-04 11:15:42  17.76437  true    false    0x4b67cf4d     MathGame                       primeFactors
                              9
 1005    2018-12-04 11:15:43  0.4776    false   true     0x4b67cf4d     MathGame                       primeFactors
Affect(row-cnt:6) cost in 4 ms.
```

#### 筛选调用记录

```bash
$ tt -s 'method.name=="primeFactors"'
 INDEX   TIMESTAMP            COST(ms)  IS-RET  IS-EXP   OBJECT         CLASS                          METHOD
-------------------------------------------------------------------------------------------------------------------------------------
 1000    2018-12-04 11:15:38  1.096236  false   true     0x4b67cf4d     MathGame                       primeFactors
 1001    2018-12-04 11:15:39  0.191848  false   true     0x4b67cf4d     MathGame                       primeFactors
 1002    2018-12-04 11:15:40  0.069523  false   true     0x4b67cf4d     MathGame                       primeFactors
 1003    2018-12-04 11:15:41  0.186073  false   true     0x4b67cf4d     MathGame                       primeFactors
 1004    2018-12-04 11:15:42  17.76437  true    false    0x4b67cf4d     MathGame                       primeFactors
                              9
 1005    2018-12-04 11:15:43  0.4776    false   true     0x4b67cf4d     MathGame                       primeFactors
Affect(row-cnt:6) cost in 607 ms.
```

### 查看调用信息

```bash
$ tt -i 1003
 INDEX            1003
 GMT-CREATE       2018-12-04 11:15:41
 COST(ms)         0.186073
 OBJECT           0x4b67cf4d
 CLASS            demo.MathGame
 METHOD           primeFactors
 IS-RETURN        false
 IS-EXCEPTION     true
 PARAMETERS[0]    @Integer[-564322413]
 THROW-EXCEPTION  java.lang.IllegalArgumentException: number is: -564322413, need >= 2
                      at demo.MathGame.primeFactors(MathGame.java:46)
                      at demo.MathGame.run(MathGame.java:24)
                      at demo.MathGame.main(MathGame.java:16)
 
Affect(row-cnt:1) cost in 11 ms.
```

### 重新发起一次调用

```bash
$ tt -i 1004 -p
 RE-INDEX       1004
 GMT-REPLAY     2018-12-04 11:26:00
 OBJECT         0x4b67cf4d
 CLASS          demo.MathGame
 METHOD         primeFactors
 PARAMETERS[0]  @Integer[946738738]
 IS-RETURN      true
 IS-EXCEPTION   false
 COST(ms)         0.186073
 RETURN-OBJ     @ArrayList[
                    @Integer[2],
                    @Integer[11],
                    @Integer[17],
                    @Integer[2531387],
                ]
Time fragment[1004] successfully replayed.
Affect(row-cnt:1) cost in 14 ms.
```

**replay的注意事项**

1. **ThreadLocal 信息丢失**

   很多框架偷偷的将一些环境变量信息塞到了发起调用线程的 ThreadLocal 中，由于调用线程发生了变化，这些 ThreadLocal 线程信息无法通过 Arthas 保存，所以这些信息将会丢失。

   一些常见的 CASE 比如：鹰眼的 TraceId 等。

2. **引用的对象**

   需要强调的是，`tt` 命令是将当前环境的对象引用保存起来，但仅仅也只能保存一个引用而已。如果方法内部对入参进行了变更，或者返回的对象经过了后续的处理，那么在 `tt` 查看的时候将无法看到当时最准确的值。这也是为什么 `watch` 命令存在的意义。
   
   ---
   
   
   # 工商银行打造在线诊断平台的探索与实践  
   
   作者 ｜ 刘慕雨   
   
   在信息系统建设方面，工商银行一直积极探索，以开放的姿态借鉴行业先进经验，旨在为客户提供更优质的金融服务和用户体验。随着分布式架构和云计算平台在工行的广泛应用，如何高效排查程序错误或性能瓶颈，是个棘手的问题。为此，我们基于Arthas建设了在线诊断平台，在保护客户信息安全的原则基础上，对相关能力做了剪裁和整合，通过Web方式支持更复杂的交互场景，在实际线上问题分析中发挥关键作用。   
   
   工行在线诊断平台：  
   ![图片1](https://user-images.githubusercontent.com/6982311/95725506-554cc400-0caa-11eb-85b0-f81c16a00530.png)  
   
   下面对工行在线诊断平台的建设做个阶段性总结，分享一下我们的建设经验、实际效果以及未来展望，也希望能给社区提供一个参考。  
   ## 传统方式排查问题的痛点
   对于后端工程师，一旦线上程序逻辑出错，问题排查如同破案，在分析研判时，问题现场的第一手信息是最珍贵的。开发人员很容易首先想到的就是阅读日志，从海量的日志中寻找蛛丝马迹，这就好比是对犯罪现场周边的视频监控录像逐一回看，非常辛苦。如果问题现场的日志记录缺失，就尝试在本地重现问题并调试解决，本地难以重现的，只能再加日志，再部署，再重现，然后再查日志，效率较低。对于复杂一些的比如程序性能问题，如何定位性能瓶颈，一不小心又要回到加日志、部署、查日志、再加日志的老路，不仅效率不高，也破坏了问题现场。  
   
   JDK提供的工具如jps、jmap、jstat、jstack、jconsole等，可以为工程师提供一些帮助。Linux操作系统的命令，如top、free、pidstat、vmstat、iostat等，也是排查问题尤其是性能调优必不可少的工具。但直接使用这些工具，对工程师的个人技术能力和经验要求较高。而且对企业来说，在生产环境直接通过命令行操作，是很敏感的行为。因此，如何在保证安全的基础上，又能像调试本地程序一样更便捷的排查分析，是个棘手的问题。  
   ## Arthas的解决方案
   2018年我们在参加一次Dubbo Meetup上，听了关于使用开源的Arthas工具排查Dubbo问题的分享。研究发现，Arthas通过JVM的Attach机制，在不影响服务连续性的情况下，实时连接到目标进程，便于工程师在线排查问题。此外，Arthas的字节码增强框架，可以通过Instrumentation技术动态修改字节码（需要Java虚拟机实现支持retransformClasses），替换成新的class，这就为在线调试提供了可能。  
   
   Arthas原理图：    
   ![图片2](https://user-images.githubusercontent.com/6982311/95726981-2e8f8d00-0cac-11eb-819f-06ecb9257442.png)  
   
   比如，使用watch命令，实时观测方法的调用情况；使用jad命令，把字节码反编辑成java代码；使用redefine命令，可以对代码做热更新，让开发人员告别不停“加日志、部署、查日志、再加日志”的套娃时代。在Arthas刚推出没多久，还提供了一个简单的Web Console功能，实际是一个以Websocket访问Arthas进程的方式，这也为我们后面建设在线诊断平台提供了思路。  
   ## 落地使用上的困难
   直接使用Arthas的命令行交互方式，不能满足金融级运维要求，在落地使用上存在一些实际的问题：  
   * **信息安全问题：** Arthas可以通过命令行方式，直接观测方法的入参、返回值，这就涉及到一些客户敏感信息，直接在生产环境使用命令行操作显然是不合适的。  
   * **学习成本问题：** 工具的安装使用存在一定的学习成本，尤其是对于新员工来说。  
   * **多人协作问题：** 当多个开发者对同一个目标进程进行诊断时，可能存在冲突，例如，一个同学正在watch某个方法，另一个同学把这个方法redefine了，那么第一个同学watch到的其实是别人替换后的代码的运行情况，这就像一个多线程访问共享资源的线程安全问题。再比如两个同学都在attach同一个进程，一个同学用完了，直接把Arthas进程shutdown（关闭），而另一个还在使用的同学就突然不能用了。  
   * **资源占用问题：** 如果开发者忘记关闭Arthas，Arthas进程会占用一定的系统资源（如内存、CPU）。当然，Arthas本身对资源开销是有很多考虑的，比如Arthas使用自定义的类加载器加载自身的类，在关闭时将引用置空，这样在下一次垃圾回收的时候，自定义类加载器加载的类就会被回收，从而避免对原有工程的影响。因此，我们认为Arthas应该在排查问题时使用，而不应该当作常规监控工具一直运行。  
   * **环境不统一问题：** 在不同的Java虚拟机实现安装和使用Arthas，可能会碰到一些奇怪的问题，比如用Hotspot虚拟机运行Arthas进程，而目标虚拟机是J9实现，attach的时候可能会出错。另一方面，生产环境云上、云下节点规模庞大，网络访问权限是严格控制的，开发人员想远程连接目标进程，涉及到权限开通、版本管理、安装程序下发等问题。  
   ## 技术方案
   我们设计了一套轻巧的架构，让开发人员以Web UI的方式，便捷、直观的使用各类在线诊断能力。那么我们是怎么做的呢？  
   ### 1.  整体架构
   整体架构大致分成在线诊断平台、在线诊断网关（后简称网关）、在线诊断进程三部分，通过一个网关集群统一代理对云上、云下节点的访问，网关提供Restful接口给在线诊断平台（后简称平台）调用。一个接口可能组合了多个Arthas指令，也可能对指令的执行结果进行剪裁和修改，处理成json格式数据返回给平台做展示。 
   
   整体架构图：  
   ![图片3](https://user-images.githubusercontent.com/6982311/95727010-35b69b00-0cac-11eb-95ac-2449a8a21382.png)  
   
   * **在线诊断平台：** 在线诊断平台是开发人员进行在线诊断的入口，平台通过Web UI的方式提供一站式在线诊断能力，支持复杂的交互场景。除了提供出色的用户体验，平台还解决了安装卸载、多人协作、用户鉴权、连接保持、操作审计等问题：   
       1. **安装卸载：** 根据用户在平台输入目标节点和进程信息，实现诊断程序的安装和卸载，具体将在下一节介绍。
       2. **多人协作：** 当多个用户同时诊断同一个进程时，对于存在冲突的功能，平台通过分布式锁实现互斥，保障同一时刻只有一个用户能使用，当然这么做的前提是平台在功能设计时考虑原子性。另外，当一个用户试图关闭一个其他用户正在使用的连接时，也会被拒绝。  
       3. **用户鉴权：** 建立审批和黑白名单机制，基于RBAC模型，同时联动CMDB，对操作人员的权限进行严格管控，普通开发者需要被授权才可使用，各类诊断功能的使用权限也和用户角色挂钩，权限控制强度从生产环境到开发环境逐渐减弱。  
       4. **连接保持：** Arthas一些命令比如dashboard，提供了实时监控目标进程整体运行情况的功能，默认每5秒刷新一次。对于这样的功能，平台的前端通过websocket与后端保持连接，后端内存中维护了一个目标对象和已连接用户的对照关系，如果多个用户同时监控某个目标对象，后端每次只会定时发送一次请求给网关。这么做的好处，是在保证了实时监控的同时，节约连接资源，避免了多人访问时，到目标服务器的不必要的重复连接。此外，为了防止用户忘记卸载诊断进程，对照关系也会同步到配置中心，当内存中某个目标对象对应的用户全部下线时，会触发一次检查，如果发现配置中心中的用户也已经全部下线，平台会自动发送shutdown请求给网关，卸载诊断进程。  
       5. **操作审计：** 通过切面的方式，对用户的各类操作进行记录和存储，达到审计的目的。  
       
   * **在线诊断网关：** 网关的作用，一是统一打通了到行内云上、云下环境的火墙；二是由于我们当时使用的Arthas版本还不支持Rest接口（最新版本已支持），返回报文都是文本的形式，网关侧在这里会对文本进行解析，处理成json格式的结构数据，方便平台侧做展示。归纳起来，网关的主要目的是统一代理对目标节点的访问，同时封装诊断能力，提供标准json结构数据的Rest接口给行内各平台访问。网关在封装诊断能力时，还会进行参数校验、超时管理、数据脱敏、文本处理等工作。  
       1. **参数校验：** 网关需要对诊断命令的参数做合法性校验，比如tt命令要控制最大观测次数防止撑爆内存，保证使用安全。  
       2. **超时管理：** 一些监听类的命令，比如watch命令，如果一直监听不到调用，需要设置超时时间，防止连接一直不被释放。  
       3. **数据脱敏：** 对于涉及客户敏感信息的命令，网关会做数据脱敏，保障客户信息安全，部分关键业务的敏感数据，网关侧会做封闭禁止访问。  
       4. **文本处理：** 网关向Arthas进程发送指令后，收到的报文是文本形式，为了便于平台侧展示，网关将非结构数据处理成结构数据，并对文本中的一些非关键数据进行剪裁。这里要注意因为telnet有个窗口大小的配置要适当调整，配置太小的话有可能导致文本不全。  
   
   * **在线诊断进程：** 也就是Arthas进程，需要考虑安装、启停、版本管理、网络隔离等问题，具体做法在下一节详细介绍。   
   ### 2. 诊断过程详解
   工程师第一次对目标服务器进行诊断时，涉及安装、启动、使用、卸载等一系列步骤，如下图所示：  
   ![图片4](https://user-images.githubusercontent.com/6982311/95727027-3d763f80-0cac-11eb-926e-ddd8ab90bff8.png)  
   
   **（1）诊断前准备：** 下图是我们的安装界面，当用户授权通过后，即可填写目标服务器信息开始安装。如果是传统虚拟机时，用户需输入虚拟机ip和目标进程关键字（如进程的pid、进程的启动类名、进程的jar名称等）；如果是容器，还需提供容器id：  
   ![图片5](https://user-images.githubusercontent.com/6982311/95727054-4830d480-0cac-11eb-9625-845bc464d1b5.png)  
   
   **（2）开始安装：** 用户点击安装后，会触发一系列操作。首先在线诊断平台发送一个探活请求给网关，网关收到请求后相应的也发送一个空指令给目标节点。平台根据响应判断，如果探活成功，则发送诊断请求开始诊断；否则发送安装请求到行内指定的管理系统（传统虚拟机/容器，后续简称通道）。  
   **（3）获取安装介质：** 以传统虚拟机为例（如果是容器则把安装介质下发到宿主机），通道去目标服务器指定目录查找诊断程序的版本文件，如果获取成功且版本是最新的，则通道直接执行启动脚本。否则，通道会通知目标服务器从公共的文件服务器上，下载最新版本的安装介质。  
   **（4）启动诊断程序：** 安装介质实际是一个压缩包，包括安装脚本、jar、版本文件等。安装介质首次下载到目标服务器后，通道对其解压并执行启动脚本。我们修改了Arthas的启动脚本，根据用户输入的进程关键字，找到目标进程，然后使用和目标进程相同的jdk版本，启动诊断程序。注意，启动时Arthas的target-ip参数被设置为网关地址，以隔离其他渠道访问，默认值127.0.0.1表示只能本地访问。  
   **（5）使用和卸载：** 诊断进程接收用户诊断指令，开始诊断。使用完用户可手工点击卸载，销毁诊断进程。
   ## 实际使用效果
   这里举几个例子，看一下平台的实际使用效果。  
   ### 1. 控制面板
   基于dashboard命令，控制面板实时展示目标进程的整体运行情况，包括线程、jvm、操作系统等，每隔10s刷新一次，用户也可选择手动刷新。线程按CPU使用率取TOP10，jvm主要展示内存分布和GC情况：  
   ![图片6](https://user-images.githubusercontent.com/6982311/95727076-4ebf4c00-0cac-11eb-9bcc-2aecce62a95f.png)  
   
   点击查询更多可以查看详情，比如监控jvm的内存及垃圾回收情况：  
   ![图片7](https://user-images.githubusercontent.com/6982311/95727103-54b52d00-0cac-11eb-8530-afca2979e892.png)  
   
   ### 2. 线程清单 
   线程清单页面按CPU使用率分页展示所有线程，支持按线程状态（如RUNNABLE、BLOCKED）过滤，在控制面板页面点击查看更多，也可以直接跳到此页面：  
   ![图片8](https://user-images.githubusercontent.com/6982311/95727122-5aab0e00-0cac-11eb-8e33-e76a2514ac4f.png)  
   
   点击某条记录，跳转到线程详情，展示线程堆栈，在堆栈中选中一个方法，则可以直接进行方法观测、方法追踪、方法追溯、方法监控、反编译等操作：  
   ![图片9](https://user-images.githubusercontent.com/6982311/95727143-626ab280-0cac-11eb-9e33-2fc1bd5b2459.png)  
   
   ### 3.方法监测
   方法监测指的是对方法运行情况的监控和观测，具体包括下面几个功能。  
   **（1）方法观测：** 支持对选中的堆栈中的方法进行观测，也就是watch命令，这里以开发环境举例（开发环境不做数据脱敏），可以实时捕获方法的输入、输出、异常堆栈、执行耗时等，并且支持指定观测次数、耗时限制等条件。比如我这里对UserServiceImpl类的findUser方法的调用情况进行观测：  
   ![图片10](https://user-images.githubusercontent.com/6982311/95727181-6e567480-0cac-11eb-9d89-31cbaf3447cf.png)  
   
   当方法抛异常时展示堆栈：  
   ![图片11](https://user-images.githubusercontent.com/6982311/95727196-71e9fb80-0cac-11eb-89f5-0b85246e4ff9.png)  
   
   **（2）方法追踪：** 方法追踪指的是追踪方法的调用栈，打印每个方法的执行耗时，基于trace命令实现，在排查性能问题时非常有用。看一下方法追踪的界面，直接高亮标记出调用栈上耗时最久的方法，也支持配置过滤规则，过滤一些不关心的方法（比如java底层代码）。在这个方法调用栈界面，又可以选中其中某个方法，进行观测、追溯、监控等操作，不同的诊断能力之间都是打通的：  
   ![图片12](https://user-images.githubusercontent.com/6982311/95727252-80381780-0cac-11eb-88f1-32b02f298706.png)    
   
   **（3）方法追溯：** 有时需要追溯方法被谁调用了，则可以使用方法追溯。比如这里通过方法追溯可以看到Dubbo的Filter处理链：  
   ![图片13](https://user-images.githubusercontent.com/6982311/95727279-875f2580-0cac-11eb-9c3d-944dd5af31c6.png)  
   
   **（4）方法监控：** 有时我们需要对某个方法一段时间的执行情况进行观测，这时可以使用方法监控功能。比如这里每隔10秒统计一次方法的执行次数、成功失败次数、平均耗时、失败率等指标：  
   ![图片14](https://user-images.githubusercontent.com/6982311/95727301-8d550680-0cac-11eb-9251-da603bb3b66d.png)  
   
   ### 4. 反编译
   当我们需要确认Java虚拟机加载的代码情况时，比如你修改的代码是否生效，我们在本地经常使用一些反编译工具以达目的。jad命令提供了在线反编译能力，我们基于jad以界面的形式展示Java虚拟机加载的实际代码，同时也会展示类加载器树、类的路径。比如这里对findUser方法的反编译情况（不指定方法名的话，则对整个类反编译），可以看到这个类属于springboot项目，被spring的类加载器加载：  
   ![图片15](https://user-images.githubusercontent.com/6982311/95727340-98a83200-0cac-11eb-8488-f29c501e4673.png)  
   
   ### 5. 其他能力
   我们对其他的实用命令，也做了一些封装和定制，比如sc、sm、redefine、tt等，这里就不一一介绍。此外，我们也将实时诊断能力和内部监控运维系统相整合，在排查问题时，可以通过直接跳转的方式申请在线诊断权限，对目标进程做实时诊断。下图是行内全链路跟踪产品截图，支持直接对问题链路关联的节点进行诊断：  
   ![图片16](https://user-images.githubusercontent.com/6982311/95727375-a1990380-0cac-11eb-8b5e-4e47d6675ee5.png)  
   
   ## 回顾与展望
   简要回顾一下前面提到的几个困难和我们的解决方案：  
   * **学习成本问题：** 我们通过Web UI的方式降低学习成本，也提供了更复杂的交互场景和出色的用户体验。通过网关统一提供Resful接口提供结构数据，也更便于集成到企业内部其他运维平台。  
   * **多人协作问题：** 通过会话管理、互斥访问、自动卸载等手段，解决多人协作问题。  
   * **信息安全问题：** 通过用户鉴权、数据脱敏、操作审计、网络隔离等手段，解决信息安全问题。  
   * **资源占用问题：** 通过自动卸载、减少连接、自定义类加载器（Arthas自带）等手段，在充分性能测试基础上，保障资源占用可控。  
   * **环境不统一问题：** 通过搭建网关集群，统一代理对云上、云下环境的访问。采用一致的策略，统一管理云上、云下环境介质的版本、下发和安装。  
   
   当前，工行在线诊断平台已在实际线上问题分析中持续发挥关键作用，Arthas也越来越得到社区的关注和开发者的认可。Arthas官方在新版本中已经支持了Rest接口，提供结构数据，也支持了更丰富的诊断命令，这跟我们的建设思路不谋而合。未来，我们将继续积极参与社区建设，将工行的实践经验分享给大家。