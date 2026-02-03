---
layout: post
title: 性能分析与调优-高性能
category: arch
tags: [ arch ]
---

## 资料参考

- 性能测试所有文章汇总：http://www.cnblogs.com/fnng/archive/2012/08/17/2644878.html

## 一、性能测试知多少

最近一直纠结性能分析与调优如何下手，先从硬件开始，还是先从代码或数据库。从操作系统（CPU调度，内存管理，进程调度，磁盘I/O）、网络、协议（HTTP，TCP/IP），还是从应用程序代码，数据库调优，中间件配置等方面入手。

单一个中间件又分web中间件（apache、IIS），应用中间件（tomcat、weblogic、webSphere）等，虽然都是中间件，每一样拎出来往深了学都不是一朝一夕之功。但调优对于每一项的要求又不仅仅是“知道”或“会使用”这么简单。起码要达到“如何更好的使用”。

性能测试不单单是性能测试工程师一个人的事儿。需要DBA、开发人员、运维人员的配合完成。

再说性能调优前，我们有必要再提一下进行测试的目的，或者我们进行性能测试的初衷是什么？

- 能力验证：验证某系统在一定条件具有什么样的能力。
- 能力规划：如何使系统达到我们要求的性能能力。
- 应用程序诊断：比如内存泄漏，通过功能测试很难发现，但通过性能测试却很容易发现。
- 性能调优：满足用户需求，进一步进行系统分析找出瓶颈，优化瓶颈，提高系统整体性能。

## 二、一般系统的瓶颈

性能测试调优需要先发现瓶颈，那么系统一般会存在哪些瓶颈：

- 硬件上的性能瓶颈：    
  一般指的是CPU、内存、磁盘I/O方面的问题，分为服务器硬件瓶颈、网络瓶颈（对局域网可以不考虑）、服务器操作系统瓶颈（参数配置）、中间件瓶颈（参数配置、数据库、web服务器等）、应用瓶颈（SQL语句、数据库设计、业务逻辑、算法等）。

- 应用软件上的性能瓶颈：  
  一般指的是应用服务器、web服务器等应用软件，还包括数据库系统。
  例如：中间件weblogic平台上配置的JDBC连接池的参数设置不合理，造成的瓶颈。

- 应用程序上的性能瓶颈：  
  一般指的是开发人员新开发出来的应用程序。
  例如，程序架构规划不合理，程序本身设计有问题（串行处理、请求的处理线程不够），造成系统在大量用户方位时性能低下而造成的瓶颈。

- 操作系统上的性能瓶颈：  
  一般指的是windows、UNIX、Linux等操作系统。
  例如，在进行性能测试，出现物理内存不足时，虚拟内存设置也不合理，虚拟内存的交换效率就会大大降低，从而导致行为的响应时间大大增加，这时认为操作系统上出现性能瓶颈。

- 网络设备上的性能瓶颈：  
  一般指的是防火墙、动态负载均衡器、交换机等设备。  
  例如，在动态负载均衡器上设置了动态分发负载的机制，当发现某个应用服务器上的硬件资源已经到达极限时，动态负载均衡器将后续的交易请求发送到其他负载较轻的应用服务器上。在测试时发现，动态负载均衡器没有起到相应的作用，这时可以认为网络瓶颈。  
  性能测试出现的原因及其定位十分复杂，这里只是简单介绍常见的几种瓶颈类型和特征，而性能测试所需要做的就是根据各种情况因素综合考虑，然后协助开发人员\DBA\运维人员一起定位性能瓶颈。

## 三、一般性能调优步骤

- 步骤一：确定问题  
  应用程序代码：在通常情况下，很多程序的性能问题都是写出来的，因此对于发现瓶颈的模块，应该首先检查一下代码。  
  数据库配置：经常引起整个系统运行缓慢，一些诸如oracle的大型数据库都是需要DBA进行正确的参数调整才能投产的。  
  操作系统配置：不合理就可能引起系统瓶颈。  
  硬件设置：硬盘速度、内存大小等都是容易引起瓶颈的原因，因此这些都是分析的重点。
  网络：网络负载过重导致网络冲突和网络延迟。

- 步骤二：确定问题原因  
  当确定了问题之后，我们要明确这个问题影响的是响应时间吞吐量，还是其他问题？是多数用户还是少数用户遇到了问题？如果是少数用户，这几个用户与其它用户的操作有什么不用？系统资源监控的结果是否正常？CPU的使用是否到达极限？I/O情况如何？问题是否集中在某一类模块中？是客户端还是服务器出现问题？系统硬件配置是否够用？实际负载是否超过了系统的负载能力？是否未对系统进行优化？  
  通过这些分析及一些与系统相关问题，可以对系统瓶颈有更深入了解，进而分析出真正的原因。

- 步骤三：确定调整目标和解决方案  
  得高系统吞吐理，缩短响应时间，更好地支持并发。

- 步骤四：测试解决方案  
  对通过解决方案调优后的系统进行基准测试。（基准测试是指通过设计科学的测试方法、测试工具和测试系统，实现对一类测试对象的某项性能指标进行定量的和可对比的测试）

- 步骤五：分析调优结果  
  系统调优是否达到或者超出了预定目标？系统是整体性能得到了改善，还是以系统某部分性能来解决其他问题。调优是否可以结束了。

## 四、性能调优维度

### 4.1.理解性能调优、衡量维度

- 并发数: 10
    - 系统能并发处理的请求数量
    - 有多少请求到达服务器，还没有处理完成返回
- 响应时间：100ms
- 吞吐量： 10/0.1=100 ，每秒处理请求数
- 性能计数器：cpu/内存/硬盘的消耗程度

### 4.2.JVM调优

#### 4.2.1 JVM参数调优

[1.启用垃圾收集器:](https://blog.csdn.net/leo187/article/details/88920036)

``` 
-XX:+UseSerialGC： 使用串行回收器进行回收，新生代和老年代都使用串行回收器，新生代使用复制算法，老年代使用标记-整理算法。Serial收集器是最基本、历史最悠久的收集器，它是一个单线程收集器。一旦回收器开始运行时，整个系统都要停止
-XX:+UseParNewGC： ParNew收集器是Serial收集器的多线程版本，在新生代进行并行回收，老年代仍旧使用串行回收。新生代S区使用复制算法。操作系统是多核CPU上效果明显，单核CPU建议使用串行回收器。打印GC详情时有ParNew标识。默认关闭
-XX:+UseParallelGC： 代表新生代使用Parallel收集器，老年代使用并行收集器
-XX:+UseParallelOldGC： 新生代和老年代都使用并行收集器。打印出的GC日志会带PSYoungGen、ParOldGen关键字
-XX:+UseConcMarkSweepGC： 并发标记清除，老年代 即使用CMS收集器。它是和应用程序线程一起执行，相对于Stop The World来说虚拟机停顿时间较少，新生代使用ParNew收集算法。默认关闭
-XX:+ UseCMSCompactAtFullCollection： Full GC后，进行一次整理，整理过程是独占的，会引起停顿时间变长。仅在使用CMS收集器时生效。
-XX:ParallelCMSThreads： 设置并行GC时进行内存回收的线程数量
-XX:PreternureSizeThreshold： 直接晋升老年代的对象大小，设置了这个参数后，大于这个参数的对象直接在老年代进行分配。
-XX:MaxTenuringThreshold： 晋升老年代的对象年龄，对象在每一次Minor GC后年龄增加一岁，超过这个值后进入到老年代。默认值为15。
-XX:NativeMemoryTracking=detail： 使用命令jcmd pid VM.native_memory detail，配合查看JVM相关情况 
```

2.堆大小配置参数：     
默认空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制；空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制

``` 
-Xms2048m：           初始总堆内存，一般设置的和最大堆内存一样大，就不需要根据当前堆使用情况而调整堆的大小了
-Xmx2048m:            最大总堆内存，一般设置为物理内存的1/4
-Xmn768m:             年轻代堆内存，推荐为整个堆的3/8
-XX:PermSize=128m:    持久带堆的初始大小
-XX:MaxPermSize=128m: 持久带堆的最大大小
-XX:NewRatio=3:       表示年轻代与年老代比值为1：3，年轻代占整个年轻代年老代和的1/4
-XX:SurvivorRatio=3:  表示Eden：Survivor=3：2，一个Survivor区占整个年轻代的1/5
```

3.设置JVM参数  
Tomcat在catalina.sh 修改JVM堆内存大小，加在首行

```
JAVA_OPTS="-server -Xms800m -Xmx800m -XX:PermSize=256m -XX:MaxPermSize=512m -XX:MaxNewSize=512m"
```

springboot 启动时加JVM参数

```
nohup java -Xms2048m -Xmx2048m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/data/java_heapdump.hprof -jar demo-server.jar --spring.profiles.active=pro
```

#### 4.2.2 GC日志分析

1.启用打印gc日志:

``` 
-XX:+printGC  虚拟机运行后，只要遇到 GC，就会打印日志    
-XX:+PrintGCDetails 获取比 -XX:+PrintGC 参数更详细的 GC 信息。   
-XX:+PrintHeapAtGC 对堆空间进行跟踪，可以在每次 GC 前后分别打印堆的信息。注意，是 GC 前后均打印，打印两次。 
-XX:+PrintGCTimeStamps 会在每次GC发生时，输出GC发生的时间，该时间为虚拟机启动后的时间偏移量。需与 -XX:+PrintGC 或 -XX:+PrintGCDetails 配合使用
```

2.配置gc日志文件路径：

``` 
-verbose:gc  // 控制台输出 
-Xloggc:gc.log  //项目根目录输出日志文件
-Xloggc:log/gc.log //指定日志文件目录
```  

3.配置示例  
本地IDEA添加参数 edit configuration -> Environent -> vm options：  
生产启动命令参数追加参数：

``` 
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc

-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -Xloggc:gc.log
```

#### 4.2.3 内存溢出dump分析

开发、测试、甚至是生产环境中，遇到OutOfMemoryError，堆溢出，表明程序有严重的问题。需找造成OutOfMemoryError原因。 一般有两种情况：

- 1、内存泄露，对象已经死了，无法通过垃圾收集器进行自动回收，通过找出泄露的代码位置和原因，才好确定解决方案；
-
2、内存溢出，内存中的对象都还必须存活着，这说明Java堆分配空间不足，检查堆设置大小（-Xmx与-Xms），检查代码是否存在对象生命周期太长、持有状态时间过长的情况。 。

1.内存溢出JVM参数配置：启动shell脚本或命令

``` 
## 启用
-XX:+HeapDumpOnOutOfMemoryError 

## dump日志文件路径
-XX:HeapDumpPath=/data/log/servername_dump.hprof
```

2.JDK工具分析dump

- jconsole：javaGUI监视工具，可以以图表化的形式显示各种数据。并可通过远程连接监视远程的服务器VM。命令行里打
  jconsole，选则进程就可以了。需要注意的就是在运行jconsole之前，必须要先设置环境变量DISPLAY，否则会报错误，Linux下设置环境变量如下：export
  DISPLAY=:0.0
- 【Jvisualvm】：也是一个基于图形化界面的、可以查看本地及远程的JAVA
  GUI监控工具，Jvisualvm同jconsole的使用方式一样，直接在命令行打入Jvisualvm即可启动，不过Jvisualvm相比，界面更美观一些，数据更实时
- Jstat：虚拟机统计信息监视工具
- Jstack：打印出给定的java进程ID或corefile或远程调试服务的Java堆栈信息，如果是在64位机器上，需要指定选项"-J-d64"
  ，Windows的jstack使用方式只支持以下的这种方式： jstack [-l] pid
- Jps：显示当前所有java进程pid命令，适合在linux/unix平台上简单察看当前java进程信息。
- 【Jmap】：打印出某个java进程（使用pid）内存中，所有‘对象’的情况（如：产生那些对象，及其数量） jmap -J-d64 -heap pid

3.MAT(Eclipse Memory Analyzer Tool)：内存分析工具

```
1.MAT安装与介绍
下载地址：http://www.eclipse.org/mat/downloads.php。
或 在 eclipse ->install new software ->http://download.eclipse.org/mat/1.6/update-site/ 进行安装

2.tomcat配置JVM，或者springboot启动参数
通过jvm参数--XX:-HeapDumpOnOutOfMemoryError可以让JVM在出现内存溢出是Dump出当前的内存转储快照；
例如：-Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/data/log/servername_dump.hprof

3.springboot 启动时加JVM参数         
nohup java -Xms2048m -Xmx2048m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/data/java_heapdump.hprof -jar demo-server.jar --spring.profiles.active=pro

4.使用示例 https://wdsheng0i.github.io/arch/2021/02/10/mat.html
```

### 4.3.Tomcat调优

- Tomcat运行机制和框架
- Tomcat线程模型
- Tomcat系统参数认识和调优
- 基准测试
- 双亲委派类加载机制

### 4.4.Mysql调优

- SQL优化
- 索引优化
- 查询缓存
- 分区、分库、分表、分片
- 读写库分离
- 分布式数据库、集群
- 主从复制、主从热备

### 4.5.程序代码优化

- IO优化
- 数据结构、算法优化
- 缓存优化
- 多线程优化
- 异步处理/异步请求

### 4.6.系统架构优化

- 池技术
- 全文检索
- 缓存技术
- 异步技术：消息队列、预计算
- 集群、负载均衡
- 分布式应用、微服务

### 4.7.WEB前端加载性能优化

- 动静分离
- 异步加载

### 4.8.Linux服务器监控、资源扩展

- 提升硬件：cpu、内存、网卡、磁盘
 

 
















