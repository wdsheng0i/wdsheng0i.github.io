---
layout: post
title: 架构师能力总结
category: arch
tags: [arch]
---
    
架构师能力总结

# 资料
- 服务架构的演变历史是怎么的https://www.zhihu.com/question/67629774
- 软件架构的演变-单体架构，垂直架构，分布式架构，SOA架构和微服务架构的演变历程
  - https://zhuanlan.zhihu.com/p/98392801
  - https://blog.csdn.net/weixin_44062339/article/details/103262680
- 从单体架构到微服务的服务化演进之路https://www.cnblogs.com/zytrue/p/8487616.html
- 分布式、集群、微服务、SOA 之间的区别  https://blog.csdn.net/HeatDeath/article/details/79038795 
- 分布式服务架构与微服务架构概念的区别与联系是怎样的？ https://www.zhihu.com/question/28253777
- 何谓分布式服务器，怎么理解分布式服务框架？ https://www.zhihu.com/question/19699884
- 分布式与集群的区别是什么？ https://www.zhihu.com/question/20004877
- SOA和微服务架构的区别？ https://www.zhihu.com/question/37808426

    
## 一. 架构基本理论
### 1.1 架构设计方法
![](https://wdsheng0i.github.io/assets/images/2021/micro/5v.png)  
- 5视图法
    - "逻辑架构"（业务架构、应用架构）
    - "开发架构"（技术架构）
    - "数据架构"（数据分类、存储）
    - "物理架构"（部署架构）
    - "运行架构"（系统对接、数据流转）
- [TOGAF](https://zhuanlan.zhihu.com/p/152088490)：(The Open Group Architecture Framework) 开放组织架构框架
- [DODAF](http://www.uml.org.cn/modeler/202108172.asp)：(Department of Defense Architecture Framework ) (美国)国防部体系结构框架
- 架构风格： 数据流风格、复制风格、分层风格、移动代码风格、点对点风格、分布式风格、管道和过滤器风格	

### 1.2 数据一致性[设计](https://www.jianshu.com/p/ac0a8363c23d)  

#### CAP定理
一致性Consistency、可用性Availability、分区容错性Partition tolerance
```
CAP定理（CAP theorem）
又被称作 布鲁尔定理（Brewer's theorem）,它指出对于一个分布式计算系统来说，不可能同时满足以下三点:
一致性(Consistency) (所有节点在同一时间具有相同的数据)
可用性(Availability) (保证每个请求不管成功或者失败都有响应)
分隔容忍(Partition tolerance) (系统中任意信息的丢失或失败不会影响系统的继续运作)

CAP理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，最多只能同时较好的满足两个。
因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三 大类：
CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。
CP - 满足一致性，分区容忍性的系统，通常性能不是特别高。
AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。
```

#### BASE
保证服务基本可用Basically Available、柔性状态Soft state、最终一致性Eventually consistent
```
BASE是NoSQL数据库通常对可用性及一致性的弱要求原则:
Basically Availble --基本可用
Soft-state --软状态/柔性事务。 "Soft state" 可以理解为"无连接"的, 而 "Hard state" 是"面向连接"的
Eventual Consistency -- 最终一致性， 也是是 ACID 的最终目的。
```

#### 两阶段提交2PC：
- 第一阶段：准备阶段(投票阶段)
- 第二阶段：提交阶段（执行阶段）

#### 三阶段提交3PC：
- CanCommit、
- PreCommit(undo和redo信息记录到事务日志中)、
- DoCommit

#### 事务ACID
- ACID特性：原子性、一致性、隔离性、持久性
- 隔离级别：读未提交（Read Uncommitted）、读已提交（Read Committed）、可重复读（Read Repeatable）、串行化（Serializable）
- 传播行为：同一事务、新启事务、抛异常...

#### 分布式一致性理论paxos、raft、zab算法

### 1.3 可用性设计：[高可用](https://zhuanlan.zhihu.com/p/375847349)
7 x 24 小时不间断服务，减少停工时间，保证服务的持续可用。  
- MTBF：平均无故障工作时间，英文全称是“Mean Time Between Failure”
- MTTR：平均修理时间(Mean Time to Repair);
- 主从、热备、灾备

### 1.4 可靠性设计：[高性能、高并发](https://zhuanlan.zhihu.com/p/375847349)
处理速度快、耗能少、支撑并发量大  
- 集群、多节点
- 分布式

### 1.5 架构设计其他属性
- 扩展性
- 可伸缩
- 简单性
- 可修改性 
- 可移植性 

### 1.6 架构设计的关注点
- "结构"
    - 子系统
    - 子系统模块
    - 使用的公用组件
    - 与外部系统关系
    - 部署方式
    - 类似UML组件图描述内容
- "行为"
    - 组件间交互方式
    - 组件间交互过程
    - 类似UML时序图描述内容
- "重要因素"
    - 只关注重要因素，不关注其他因素
    - 判断标准
        - 独特点（业务因素）
        - 创建和改变的的开销大小（经济因素）
        - 业务重点
        - 技术难点
    - 所有重要因素合一起，即系统的抽象
    - 若重要因素选取适当，今后需求改动，系统改动较小，是衡量优秀架构的标准
- "架构风格"
    - 架构风格类似设计模式
    - 一个系统可以使用多个架构风格
    - 利用架构风格可以使架构师的生活变得更容易一些
        - 不需要多考虑风险，一般经过实践验证的
        - 不需要描述文档，每种风格都备有描述文档
    - 常见风格
        - 分布式风格、管道和过滤器风格、
- "利益相关者": 每个角色都关注系统不同特性，甚至大多数是非功能方面的，架构师需要与这些因素权衡 
    - 最终用户
    - 系统管理员
    - 消费者
    - 市场人员。销售人员
    - 开发人员（包括架构师自己）
    - 开发团队管理人员
    - 开发团队维护人员

## 二. 架构设计能力
### 2.1 面向对象程序设计（类设计原则、设计模式）
- 设计原则（类设计）
- 设计模式

### 2.2 算法设计

### 2.3 数据库设计
- 表字段设计、关联设计、外键设计
- 冗余设计
- 索引设计
- 增量设计
- 非结构化存储设计

### 2.4 性能设计
- 代码级别
    - 并发：多线程、乐观锁、悲观锁
    - 数据库索引
    - 全文检索
    - 优化算法、数据结构
    - IO优化
- 系统架构
    - 缓存技术
    - 池技术
    - 预计算
    - 异步化
    - 动静分离
    - 读写分离
    - 分区、分库、分表
    - 消息队列
    - 分布式、微服务、集群 
- 提升硬件：cpu、内存、网卡、磁盘

### 2.5 安全设计
- 安全风险
    - xss
    - CSRF
    - session超期
    - 越权访问
    - https
    - sql注入
    - 页面敏感信息隐位
- 应对方案
- 安全规范：总结风险及对应方案，作为开发规范要求点
- 安全框架
    - SpringSecurity
    - Shiro
- 加密算法
    - 签名：DSA
    - 摘要算法：MD5算法、SHA算法
    - 编码和解码：Base64编码、Hex十六进制编码
    - 对称加密：DES算法、AES算法
    - 非对称加密：RSA算法、SM4
- 认证与授权
    - 用户密码
    - 万能key
    - 特殊令牌
    - token
    - JWT：Json Web Token
    - SSO：单点登录
    - OAth认证
- 防攻击
    - 防恶意请求
    - 防sql注入
    - 防跨站请求伪造

### 2.6 高并发、高可用方案设计
集群、分布式、微服务架构设计  
- 1).高可用部署方案：LVS + Keepalived + Nginx实现 **动静分离、反向代理、集群、负载均衡、主从热备、双机主备** 
- 2).高可用redis缓存方案：主从复制、Redis集群、哨兵监控
- 3).高可用DB数据库方案：Mycat配置实现-mysql集群、主从复制、读写分离、负载均衡、分库分表
- 4).高并发下解耦、削峰、异步方案：消息队列kafka
- 5).高并发下服务降级、限流方案
- 6).CND加速静态文件访问
- 7).应用容灾及机房规划
- 8).系统扩容机制 

## 三. 设计文档
### 3.1 系统总体（架构）设计文档

### 3.2 概要设计文档

### 3.3 详细设计文档

### 扩展：UML统一建模语言(Unified Modeling Language)
- 用例图：角色以及角色与用例之间的连接关系 
- 类图：类，类、接口之间的关系的静态视图。 
- 对象图：对象之间的关系
- 包图：
- 活动图：描述用例所要进行的活动，及活动间的约束关系 
- 状态图：描述对象所有可能的状态，及状态的转移条件
- 序列图(顺序图)：参与者如何以一系列顺序的步骤与系统的对象交互的模型。 
- 协作图：和序列图相似，显示对象间的动态合作关系
- 构件图(组件图)：描述代码构件的物理结构及各种构建间依赖关系。 
- 部署(配置图)：用来建模系统的物理部署。 

## 四. 分布式架构
### 4.1 分布式中间件
- 分布式消息中间件：kafka
- 分布式缓存：Redis
- 分布式数据库:　mycat
- 分布式服务：Dubbo
- 注册中心: zookeeper
- 分布式文件系统：MongoDB
- 分布式文件存储服务：Minio/Fastdfs
- 负载均衡、限流：Nginx
- 高性能NIO框架：Netty
- 分布式日志搜索引擎：ELK

### 4.2 分布式解决方案
- 分布式session会话保持方案、跨域共享、企业级单点登录方案  
	- 代理
	- session复制
	- session绑定
	- 客户端缓存
	- session服务器-靠谱
- 分布式全局id生成方案  
- 分布式事务解决方案 & 数据一致性    
	- 分布式事务框架LCN
	- 阿里开源分布式事务框架FESCR
	- 柔性事务和刚性事务
	- TCC补偿机制
- 分布式锁解决方案  
	- 基于数据库
	    - 基于表字段版本号做分布式锁
	    - 基于数据库排他锁做分布式锁
	- 基于Redis实现
	    - 基于 REDIS 的 SETNX()、EXPIRE() 做分布式锁
	    - 基于 REDIS 的 SETNX()、GET()、GETSET()做分布式锁
	    - 基于 REDLOCK 做集群模式的 Redis 分布式锁
	- 基于zookeeper实现
- 分布式负载均衡方案  
	- 硬件: F5
	- 软件: LVS、Nginx、HAProxy、Ribbon
- 分布式任务调度方案  
	- Quartz
	- xxl-job
	- elastic-job
	- saturn
	- TBSchedule
- 分布式RPC通信解决方案：  
	- 基于消息方式的系统间通信
	- 基于框架RPC通信
		- Webservice-CXF实现
		- RMI-Spring RMI实现
		- Hessian
	- 从网络通信探究分布式通信原理
	- 理解通信协议传输过程中的序列化反序列化
	- 传统RPC技术在分布式下面临的问题
- 分布式接口幂等性  
- 分布式限流-Guava RateLimiter 

## 五. 微服务架构 
### 5.1 微服务架构设计理论
- 微服务开发设计
    - 服务划分、拆分
    - API设计、管理
	- 会话保持方案session: 代理/session复制/session绑定/客户端缓存/session服务器-靠谱
	- 分布式事务
	- 分布式锁
    - 缓存设计
    - MQ设计
- 服务安全
	- OAth认证
	- jwt授权
	- IAM
- 服务运行支撑
    - 服务注册、发现
    - 服务调度
    - 网关
    - 负载均衡
    - 服务配置（配置中心）
- 服务容错
    - 访问控制
    - 限流
    - 降级
    - 超时
    - 隔离
    - 熔断
- 服务监控
    - 日志监控
    - 调用链监控
    - Metrucs监控
    - 告警通知
    - 健康检查
- 服务部署发布
    - 发布机制：蓝绿/金丝雀/灰度

### 5.2 微服务架构实践-springcloud
- springboot 集成通用功能组件 
- springcloud 组件集成 
	- Eureka注册中心
	- Ribbon集成Rest试下负载均衡
	- Feign声明式服务调用
	- Hystrix服务熔断降级方式
	- Zuul实现服务网关
	- Config分布式统一配置中心
	- actuator服务监控组件
	- Sieuth调用链路跟踪
	- BUS消息总线
- [spring-cloud-alibaba-ability](https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md) 
    - spring cloud alibaba Sentinel 流量控制、熔断降级、系统负载保护
    - spring cloud alibaba nacos 配置中心、服务注册、发现
    - Spring Cloud Alibaba Seata 处理分布式事务  
- Docker & K8s
- 服务网格（Service Mesh）

## 六.并发编程 && JVM
- JVM虚拟机
    - 运行时数据区域
    - 类加载机制
    - 垃圾回收机制、GC算法
- JVM内存模型（JMM）  
    - java当中的线程通讯和消息传递  
    - 什么是重排序和顺序一致性？
    - Happens-Before？
    - As-If-Serial？
- Volatile和DCL的知识  
    - Volatile的使用场景和Volatile实现机制、内存语义、内存模型  
    - DCL的单例模式，什么是DCL？如何来解决DCL的问题
- 并发基础之AQS的深度分析  
    - AbstractAueuedSynchronizer同步器的概念、CLH同步队列是什么？  
    - 同步状态的获取和释放、线程阻塞和唤醒
- Synchronized的概念和分析  
    - 同步、重量级锁以及Synchronized的原理分析  
    - 自旋锁、偏向锁、轻量级锁、重量级锁的概念、使用以及如何来优化他们
- Lock和并发常用工具类  
    - 锁：Lock、ReentrantLock、ReentrantReadWriteLock、Condition  
    - 并发工具类CyclicBarrier、CountDownLatch、Semphore  
    - 并发集合类ConcurrentHashMap、ConcurrentLinkedQueue......
- 原子操作常用知识讲解  
    - 基本类型的原子操作比如经典的AtomicBoolean、AtomicLnteger、AtomicLong  
    - 数组类型的原子操作代表几个类AtomicIntegerArray、AtomicLongArray、AtomicReferenceArray  
    - 引用类型的原子操作的典型AtomicReference、AtomicReferenceFieldUpdater......  
    - CAS的概念和知识、Compare And Swap 以及他的缺陷
- 线程池和并发并行  
    - Executor
    - ThreadPoolExecutor
    - Callable & Future
    - ScheduledExecutorService  
    - ThreadLocal、
    - Fork & Join？
    - 什么是并行？
    - 线程池如何保证核心线程不被销毁？

## 七.框架和源码
- 设计模式 && 设计原则
- spring源码分析 
- mybatis应用和源码解析 
- tomcat源码解析 
- netty源码分析
- redis源码分析

## 八.性能调优
- JVM性能调优 
- Mysql性能调优  
- Java数据结构算法优化  
- Web调优
- 系统架构优化
- Tomcat调优
- Linux服务器调优

## 九.工程化
### 9.1 Dev & Ops
- Nexus搭建maven私服
- Gitlab 部署、Gitflow、CI/CD 配置
- jenkins部署、配置项目自动化构建
- Sonar部署、配置项目git、配置静态代码检查规则
- IDEA激活、设置、使用、常用插件
- Yapi搭建、接口维护
- JIRA搭建、缺陷管理
- Docker & k8s部署、使用
- ELK 部署、使用
- APM 部署、使用

### 9.2 Linux服务器 & 中间件
- centos 系统安装
- jdk 安装、配置
- tomcat 安装、配置
- mysql 安装、配置
- mycat 安装、配置
- zookeeper 安装、配置
- redis 安装、配置
- kafka 安装、配置
- mongodb 安装、配置
- nginx 安装、配置 

## 十.架构师主要工作
- 设计、搭建
	- 软件系统架构（平台、数据库、接口和应用架构等），解决开发中各种系统架构问题。
- 核心开发
	- 核心功能模块设计、核心代码开发
- 优化架构
	- 在项目需求不断细化的工程中校正整体的架构设计，以及详细模块拆分设计。
- 攻坚克难
	- 带领团队攻克例如大数据量、高并发、高稳定性等带来的各种挑战及技术难关。
- 优化性能
	- 分析、解决软件系统平台性能问题。
- 流程、规范
	- 带领团队不断完善**开发方法及开发流程**，提升开发效率与质量，加强**技术标准及规范**。
- 技术氛围
	- 营造技术学习氛围，帮助提升团队人员技术成长
- 技术敏感度
	- 参与讨论公司产品发展方向，完整的规划和把握产品研发架构。
- 团队责任
	- 责任心强，有团队合作精神，工作认真负责高效并具有一定抗压能力。

## 十一.架构师能力要求
- 架构能力
- 工程能力
- 业务能力
	- 行业业务
	- 需求捕获、分析
	- 总结归纳
	- 汇报、演示
- 研发管理能力 
- 文档阅读、编写能力
- 发现、解决问题能力
- 沟通能力
- 学习能力
- 影响力

