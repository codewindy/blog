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