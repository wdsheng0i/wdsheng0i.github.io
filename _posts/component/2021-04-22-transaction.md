---
layout: post
title: 事务
category: database
tags: [database]
---

事务

## 参考资料 
- [深入理解分布式事务](https://www.cnblogs.com/biakia/p/6195142.html)
- [真赞！阿里开源的这款分布式事务框架,不愧为民族之光](https://mp.weixin.qq.com/s/TKiO71lVziCCicYs5b-lPA)
 

## 一、数据库事务介绍
事务（Transaction)是由一系列对系统中数据进行访问与更新的操作所组成的一个程序 执行逻辑单元（Unit)。  

事务是应用程序中一系列严密的操作，所有操作必须成功完成，否则在每个操作中所作的所有更改都会被撤消。也就是事务具有原子性，一个事务中的一系列的操作要么全部成功，要么一个都不做。

## 二、事务特性
ACID特性： 原子性(Atomicity)、一致性(Correspondence)、隔离性(Isolation)、持久性(Durability)。

- （1）原子性：整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
- （2）一致性：在事务开始之前和事务结束以后，数据库的完整性约束没有被破坏。
- （3）隔离性：隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作。如果有两个事务，运行在相同的时间内，执行 相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统。这种属性有时称为串行化，为了防止事务操作间的混淆，必须串行化或序列化请求，使得在同一时间仅有一个请求用于同一数据。
- （4）持久性：在事务完成以后，该事务所对数据库所作的更改便持久的保存在数据库之中，并不会被回滚

## 三、事务隔离级别
https://blog.csdn.net/qq_27185561/article/details/112619232

隔离级别：读未提交（Read Uncommitted）、读已提交（Read Committed）、可重复读（Read Repeatable）、串行化（Serializable）。

- Read Uncommitted：在A、B事务共同进行时，B事务修改后但未提交的内容能被A事务读到，这就会导致脏读，因为B事务有可能会回滚。
- Read Committed：在A事务先查询数值结果为1，B事务修改数值为2然后提交，接下来A事务再次查询数值结果为2，导致数据不可重复读。
- Read Repeatable：在A事务先查询数值结果为1，B事务修改数值为2然后提交，接下来A事务再次查询数值结果还是为1。
- Serializable：Read Uncommitted、Read Committed和Read Repeatable都会有一个问题：幻读，Read Committed和Read Repeatable都是针对两个事务同时对某条数据修改，但是幻读针对的是插入，例如A事务先把所有行的某个字段都修改为了2，然后B事务插入了一条数据，那个字段的值是1。A事务再查询数据时会发现包含一条1的数据。想要把幻读解决，那就需要采取Serializable，所有事务都串行执行，不允许多个事务并行操作。

## 四、事务传播行为
事务的第一个方面是传播行为（propagation behavior）。当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。 

例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。Spring定义了七种传播行为：

![](https://wdsheng0i.github.io/assets/images/2021/mysql/tra-1.png)

## 五、spring事务@Transactional
http://blog.csdn.net/trigl/article/details/50968079
> @Transactional设置：
- propagation：事务传播性设置，Propagation枚举类型。Spring支持的事务传播属性包括7种：
    - PROPAGATION_MANDATORY：方法必须在事务中执行，否则抛出异常。
    - PROPAGATION_NESTED：使方法运行在嵌套事务中，否则和PROPAGATION_REQUIRED一样。
    - PROPAGATION_NEVER ：当前方法永远不在事务中运行，否则抛出异常。
    - PROPAGATION_NOT_SUPPORTED：定义为当前事务不支持的方法，在该方法执行期间正在运行的事务会被暂停
    - PROPAGATION_REQUIRED：当前的方法必须运行在事务中，如果没有事务就新建一个事务。新事务和方法一起开始，随着方法返回或者抛出异常时终止。
    - PROPAGATION_REQUIRED_NEW ：当前方法必须新建一个事务，如果当前的事务正在运行则暂停。
    - PROPAGATION_SUPPORTS ：规定当前方法支持当前事务，但是如果没有事务在运行就使用非事务方法执行。
- isolation：事务隔离性级别设置，Isolation枚举类型
    - ISOLATION_DEFAULT ：使用数据库默认的隔离级别
    - ISOLATION_COMMITTED：允许其他事务已经提交的更新（防止脏读取）
    - ISOLATION_READ_UNCOMMITTED：允许读取其他事务未提交的更新，会导致三个缺陷发生。执行速度最快
    - ISOLATION_REPEATABLE_READ ：除非事务自身更改了数据，否则事务多次读取的数据相同（防止脏数据，多次重复读取）
    - ISOLATION_SERIALIZABLE：隔离级别最高，可以防止三个缺陷，但是速度最慢，影响性能。
- readOnly：读写性事务，只读性事务，布尔型
    - 对数据库的操作中，查询是使用最频繁的操作，每次执行查询时都要从数据库中重新读取数据，有时多次读取的数据都是相同的，这样的数据操作不仅浪费了系统资源，还影响了系统速度。对访问量大的程序来说，节省这部分资源可以大大提    升系统速度。
    - 将事务声明为只读的，那么数据库可以根据事务的特性优化事务的读取操作
- timeout：超时时间，单位秒
事务可能因为某种原因很长时间没有反应，这期间可能锁定了数据库表，影响性能。设置超时时间，如果超过该时间，事务自动回滚。
- rollbackFor：一组异常类的实例，遇到时必须进行回滚
- rollbackForClassname：一组异常类的名字，遇到时必须进行回滚
- noRollbackFor：一组异常类的实例，遇到时必须不回滚
- noRollbackForClassname：一组异常类的名字，遇到时必须不回滚

> 如何改变默认规则：  
- 1 让checked例外也回滚：在整个方法前加上 @Transactional(rollbackFor=Exception.class)
- 2 让unchecked例外不回滚： @Transactional(notRollbackFor=RunTimeException.class)
- 3 不需要事务管理的(只查询的)方法:@Transactional(propagation=Propagation.NOT_SUPPORTED

## 六、Mysql下的MVCC
MVCC，Multi-Version Concurrency Control，多版本并发控制。MVCC 是一种并发控制的方法，一般在数据库管理系统中，实现对数据库的并发访问，在编程语言中实现事务内存。

基于innodb引擎下，会在表中添加三个隐藏字段：
- DB_TRX_ID：数据创建的事务id（6 个字节，可以通过“show engine innodb status”查找）
- DB_ROLL_PT：回滚指针，指向写到rollback segment（回滚段）的一条undo log记录（7 个字节）
- DB_ROW_ID: 行标识（隐藏单调自增 ID），假如表中没有主键，InnoDB会自动生成一个隐藏主键，因此会出现这个列。另外，每条记录的头信息（record header）里都有一个专门的 bit（DELETE_FLAG）来表示当前记录是否已经被删除。

基于MVCC下操作如下：

- INSERT：创建一条数据，DB_TRX_ID的值为当前事务 id, DB_ROLL_PT为 null 。
- UPDATE：复制出需要修改的一行数据，然后把这一行数据的 DB_TRX_ID置为当前事务的 id，DB_ROLL_PT指向复制前的那一条，旧数据在undo log里面。
- DELETE：复制出需要删除的一行数据，然后把这一行的 DB_TRX_ID置为当前事务的 id，DB_ROLL_PT指向复制前的那一条。并把 DELETE_FLAG改为 true 。

RR隔离级别下：在每个事务开始的时候，会将当前系统中的所有的活跃事务拷贝到一个列表中(read view) ，事务内开始时创建Read View ， 在事务结束这段时间内 每一次查询都不会重新重建Read View （简单来说，在事务开始的时候，记录当前事务ID（是自增）,生成一个快照，这个快照包含都是当前事务ID前的数据，所以看不到事务执行期间其他事务发生提交的数据）。

RC隔离级别下：在每个查询语句开始的时候，会将当前系统中的所有的活跃事务拷贝到一个列表中(read view) ，事务内的每个查询语句都会重新创建Read View（在事务中，每次查询都会重新创建快照，所以能看到事务执行期间其他事务发生提交的数据）。 

## 七、事务类型
- 1、显式事务
    - 需要我们手动的提交或回滚；
    - DML语言所有的操作都是显示事务；
- 2、隐式事务
    - 数据库自动提交不需要我们做任何处理，同时也不具备回滚性；
    - DDL、DCL语言都是隐式事务操作；

## 八、问题
- 为什么不加@EnableTransactionManagement，也能使用@Transaction  
META-INF/spring.factories 默认自动配置


