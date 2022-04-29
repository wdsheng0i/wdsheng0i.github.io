---
layout: post
title: 《大数据开发工程师》阶段三：Spark + Sqoop + 数据仓库项目 
category: big-data
tags: [big-data]
---

Spark + Sqoop + 综合项目：电商数据仓库设计与实战  

## 阶段三：Spark + Sqoop + 综合项目：电商数据仓库设计与实战  
### 第9周   7天极速掌握Scala语言   
1、快速了解Scala  

2、Scala环境安装配置  

3、Scala中的变量和数据类型  

4、Scala中的表达式和循环  

5、Scala集合体系之Set+List+Map  

6、Scala中的Array和Tuple  

7、Scala中函数的使用  

8、Scala面向对象之类的使用  

9、Scala面向对象之对象和伴生对象  

10、Scala面向对象之apply和main的使用  

11、Scala面向对象之接口的使用  

12、Scala函数式编程之匿名函数和高阶函数的使用  

13、Scala高级特性之模式匹配和隐式转换  

  

### 第10周   内存计算Spark-快速上手   
1、快速了解Spark    
- Spark是一个用于大规模数据处理的统一计算引擎  
- Spark不仅仅可以做类似于MapReduce的离线数据计算，还可以做实时数据计算，并且它还可以实现类似于Hive的SQL计算  
- Spark是一个基于内存的计算引擎，它的计算速度可以达到MapReduce的几十倍甚至上百倍  

Spark特点:    
- 1.Speed：速度快 
    - 基于内存进行计算，它的计算性能理论上可以比MapReduce快100倍
- 2.Easy of Use：易用性  
    - 可以使用多种编程语言快速编写应用程序，例如Java、Scala、Python、R和SQL
    - Spark提供了80多个高阶函数，可以轻松构建Spark任务。
- 3.Generality：通用性, 是一个具备完整生态圈的技术框架     
    - Spark提供了Core、SQL、Streaming、MLlib、GraphX等技术组件，可一站式地完成大数据领域的离线批处理、SQL交互式查询、流式实时计算，机器学习、图计算等常见的任务
- 4.Runs Everywhere：到处运行
    - 可在Hadoop YARN、Mesos或Kubernetes上使用Spark集群。
    - 可以访问HDFS、Alluxio、Cassandra、HBase、Hive和数百个其它数据源中的数据   

Spark vs Hadoop:      
- 1.综合能力
    - Spark是一个综合性质的计算引擎; Hadoop既包含MapReduce(计算引擎)，还包含HDFS(分布式存储)和Yarn(资源管理)
- 2.计算模型
    - Spark 任务可包含多个计算操作，轻松实现复杂迭代计算; Hadoop中的MapReduce任务只包含Map和Reduce阶段，不够灵活
- 3.处理速度
    - Spark 任务的数据是基于内存的，计算速度很快; Hadoop中MapReduce 任务是基于磁盘的，速度较慢

Spark+Hadoop：    
![](https://wdsheng0i.github.io/assets/images/2021/big-data/spark-hadoop.png)    
- 底层是Hadoop的HDFS和YARN
- Spark core指的是Spark的离线批处理
- Spark Streaming指的是Spark的实时流计算
- Spark SQL指的是Spark中的SQL计算
- Spark Mlib指的是Spark中的机器学习库，这里面集成了很多机器学习算法
- Spark GraphX是指图计算

Spark的应用场景:    
- 1.低延时的海量数据计算需求，这个说的就是针对Spark core的应用
- 2.低延时SQL交互查询需求，这个说的就是针对Spark SQL的应用
- 3.准实时(秒级)海量数据计算需求，这个说的就是Spark Streaming的应用

```
注意：批处理其实就是离线计算，流处理就是实时计算，只是说法不一样罢了，意思是一样的
```  

2、Spark 集群安装部署(Standalone+ON YARN) 
Spark集群有多种部署方式，比较常见的有Standalone模式和ON YARN模式，实际工作中都会使用Spark ON YARN模式  
- Standalone模式: 是部署一套独立的Spark集群，后期开发的Spark任务在这个独立的Spark集群中执行
- ON YARN模式: 是使用现有的Hadoop集群，后期开发的Spark任务会在这个Hadoop集群中执行， 此时这个Hadoop集群就是一个公共的，不仅可以运行MapReduce任务，还可以运行Spark任务

Standalone：一主两从的集群部署
```
//1.下载
http://spark.apache.org/downloads.html
https://archive.apache.org/dist/spark/

//2.主机规划：主节点： bigdata01 从节点： bigdata02,bigdata03

//3.基础环境：需要确保这几台机器上的基础环境是OK的，防火墙、免密码登录、还有JDK

//4.先在bigdata01上部署
上传spark-2.4.3-bin-hadoop2.7.tgz上传到bigdata01的/data/soft目录中

解压：[root@bigdata01 soft]# tar -zxvf spark-2.4.3-bin-hadoop2.7.tgz

重命名spark-env.sh.template
[root@bigdata01 soft]# cd spark-2.4.3-bin-hadoop2.7/conf/
[root@bigdata01 conf]# mv spark-env.sh.template spark-env.sh

//修改 spark-env.sh末尾增加
export JAVA_HOME=/data/soft/jdk1.8
export SPARK_MASTER_HOST=bigdata01

//重命名slaves.template
[root@bigdata01 conf]# mv slaves.template slaves

// 修改slaves将文件末尾的localhost去掉，增加bigdata02和bigdata03这两个从节点的主机名
bigdata02
bigdata03

//5.将修改好配置的spark安装包，拷贝到bigdata02和bigdata03上
[root@bigdata01 soft]# scp -rq spark-2.4.3-bin-hadoop2.7 bigdata02:/data/soft
[root@bigdata01 soft]# scp -rq spark-2.4.3-bin-hadoop2.7 bigdata03:/data/soft

//6.启动Spark集群
[root@bigdata01 soft]# cd spark-2.4.3-bin-hadoop2.7
[root@bigdata01 spark-2.4.3-bin-hadoop2.7]# sbin/start-all.sh

//7.验证
bigdata01上执行jps，能看到Master进程
bigdata02和bigdata03上执行jps，能看到Worker进程

访问主节点的8080端口来查看集群信息：http://bigdata01:8080/

//8.向这个Spark独立集群提交一个spark任务
./bin/spark-submit \
  --class org.apache.spark.examples.SparkPi \
  --master spark://207.184.161.138:7077 \
  --executor-memory 20G \
  --total-executor-cores 100 \
  /path/to/examples.jar \
  1000

//9.访问主节点的8080端口来查看任务执行信息：http://bigdata01:8080/

//10.停止Spark集群，在主节点 bigdata01上执行
[root@bigdata01 spark-2.4.3-bin-hadoop2.7]# sbin/stop-all.sh
```

ON YARN部署模式：先保证有一个Hadoop集群，然后只需要部署一个Spark的客户端节点即可，不需要启动任何进程（hadoop集群要保持启动）
```
注意：Spark的客户端节点同时也需要是Hadoop的客户端节点，因为Spark需要依赖于Hadoop
使用bigdata04来部署spark on yarn，因为这个节点同时也是Hadoop的客户端节点
//1. 将spark-2.4.3-bin-hadoop2.7.tgz上传到bigdata04的/data/soft目录中

//2. 解压
[root@bigdata01 soft]# tar -zxvf spark-2.4.3-bin-hadoop2.7.tgz

//3. 重命名spark-env.sh.template
[root@bigdata01 soft]# cd spark-2.4.3-bin-hadoop2.7/conf/
[root@bigdata01 conf]# mv spark-env.sh.template spark-env.sh

//4. 修改 spark-env.sh末尾增加这两行内容，指定JAVA_HOME和Hadoop的配置文件目录
export JAVA_HOME=/data/soft/jdk1.8
export HADOOP_CONF_DIR=/data/soft/hadoop-3.2.0/etc/hadoop

//5. 提交任务，通过这个spark客户点节点，向Hadoop集群上提交spark任务
[root@bigdata04 spark-2.4.3-bin-hadoop2.7]# bin/spark-submit --class org.apache .spark.examples.SparkPi  --master yarn --deploy-mode cluster  --executor-memory 20G  --num-executor 50  /path/to/examples.jar  1000

//6. 可以到YARN的8088界面查看提交上去的任务信息
```

3、Spark工作原理分析  
![](https://wdsheng0i.github.io/assets/images/2021/big-data/spark-yuanli.png)      
- 首先通过Spark客户端提交任务到Spark集群，
- 然后Spark任务在执行的时候会读取数据源HDFS中的数据，
- 将数据加载到内存中，转化为RDD（RDD其实是一个弹性分布式数据集，是一个逻辑概念，可以先理解为是一个数据集合），
- 然后针对RDD调用一些高阶函数对数据进行处理，
- 中间可以调用多个高阶函数，
- 最终把计算出来的结果数据写到HDFS中。

4、什么是RDD
- RDD通常通过Hadoop的HDFS文件进行创建，也可以通过程序中的集合来创建
- RDD是Spark提供的核心抽象，全称为Resillient Distributed Dataset，即弹性分布式数据集

RDD的特点:  
- 弹性：RDD数据默认存放在内存中，但在内存资源不足时，Spark也会自动将RDD数据写入磁盘
- 分布式：RDD在抽象上来说是一种元素数据的集合，它是被分区的，每个分区分布在集群中的不同节点上，从而让RDD中的数据可以被并行操作
- 容错性：RDD最重要的特性就是提供了容错性，可以自动从节点失败中恢复过来

如果某个节点上的RDD partition，因为节点故障，导致数据丢了，那么RDD会自动通过自己的数据来源重新计算该partition的数据

5、Spark架构原理  
以Spark的standalone集群为例进行分析Spark架构相关的进程信息  
- Driver：编写的Spark程序就在Driver(进程)上，由Driver进程负责执行
- Master：集群的主节点中启动的进程，主要负责集群资源的管理和分配，还有集群的监控等
- Worker：集群的从节点中启动的进程，主要负责启动其它进程来执行具体数据的处理和计算任务
- Executor：此进程由Worker负责启动，主要为了执行数据处理和计算
- Task：是一个线程 由Executor负责启动，它是真正干活的  
![](https://wdsheng0i.github.io/assets/images/2021/big-data/spark-arch.png)     
1).首先在spark的客户端机器上通过driver进程执行我们的Spark代码，当我们通过spark-submit脚本提交Spark任务的时候Driver进程就启动了。  
2).Driver进程启动之后，会做一些初始化的操作，会找到集群master进程，对Spark应用程序进行注册  
3).当Master收到Spark程序的注册申请之后，会发送请求给Worker，进行资源的调度和分配  
4).Worker收到Master的请求之后，会为Spark应用启动Executor进程，启动一个或者多个Executor，具体启动多少个，会根据你的配置来启动  
5).Executor启动之后，会向Driver进行反注册，这样Driver就知道哪些Executor在为它服务了  
6).Driver会根据我们对RDD定义的操作，提交一堆的task去Executor上执行；task里面执行的其实就是具体的map、flatMap这些操作。    

6、Spark项目开发环境配置  
创建一个maven项目，集成java和scala的sdk  
时项目中默认带有java的sdk，需要添加scala的sdk:项目右键-open module setting-module-dependencies

7、WordCount代码开发(Java+Scala)  

8、Spark任务的三种提交方式  

9、Spark开启historyServer服务  

10、创建RDD的三种方式  

11、Transformation和Action介绍  

12、Transformation操作开发实战  

13、Action操作开发实战  

14、RDD持久化原理  

15、RDD持久化开发实战  

16、共享变量之Broadcast Variable的使用  

17、共享变量之Accumulator的使用  

18、案例实战：TopN主播统计  

19、面试题  

  

### 第11周   Spark + SparkSQL 性能优化的道与术   
1、宽依赖和窄依赖  

2、Stage的理解  

3、Spark任务的三种提交模式  

4、Shuffle介绍  

5、三种Shuffle机制分析  

6、checkpoint概述  

7、checkpoint和持久化的区别  

8、checkpoint代码开发和执行分析  

9、checkpoint源码分析之写操作和读操作  

10、Spark程序性能优化分析  

11、高性能序列化类库Kryo的使用  

12、持久化或者checkpoint  

13、JVM垃圾回收调忧  

14、提高并行度  

15、数据本地化  

16、算子优化  

17、SparkSQL快速上手使用  

18、实战：SparkSQL实现TopN主播统计  

  

### 第12周   数据转换工具Sqoop + 综合项目：电商数据仓库之用户行为数仓  
1、项目效果展示  

2、项目的由来  

3、什么是数据仓库  

4、数据仓库基础知识  

5、数据仓库分层  

6、典型数仓系统架构分析  

7、技术选型  

8、整体架构设计  

9、服务器资源规划  

10、生成用户行为数据【客户端数据】  

11、生成商品订单相关数据【服务端数据】  

12、采集用户行为数据【客户端数据】  

13、数据转换工具-Sqoop安装部署  
![](https://wdsheng0i.github.io/assets/images/2021/big-data/sqoop1.png)  
![](https://wdsheng0i.github.io/assets/images/2021/big-data/sqoop2.png)

14、Sqoop之数据导入功能  

15、Sqoop之数据导出功能  

16、采集商品订单相关数据【服务端数据】  

17、用户行为数据数仓开发之ods层开发  

18、用户行为数据数仓开发之ods层脚本抽取  

19、用户行为数据数仓开发之dwd层开发  

20、用户行为数据数仓开发之dwd层脚本抽取  

21、用户行为数据数仓需求分析  

22、用户行为数据数仓需求开发  

23、用户行为数据数仓表和任务脚本总结  

  

### 第13周   综合项目：电商数据仓库之商品订单数仓   
1、商品订单数据数仓开发之ods层和dwd层  

2、商品订单数据数仓需求分析与开发  

3、什么是拉链表  

4、如何制作拉链表  

5、【实战】基于订单表的拉链表实现  

6、拉链表的性能问题分析  

7、商品订单数据数仓表和任务脚本总结  

8、数据可视化之Zepplin的安装部署和参数配置  

9、数据可视化之Zepplin的使用  

10、任务调度之Crontab调度器的使用  

11、任务调度之Azkaban的安装部署  

12、任务调度之Azkaban提交独立任务  

13、任务调度之Azkaban提交依赖任务  

14、任务调度之在数仓中使用Azkaban  

15、项目优化  

  
