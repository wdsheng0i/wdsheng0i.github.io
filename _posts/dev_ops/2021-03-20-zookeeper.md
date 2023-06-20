---
layout: post
title: ZooKeeper
category: dev-ops
tags: [dev-ops]
---

ZooKeeper

## 参考资料 

## 一、简介
[Zookeeper 3、Zookeeper工作原理（详细）](https://www.cnblogs.com/raphael5200/p/5285583.html)  
ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，是Hadoop和Hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。 

ZooKeeper的目标就是封装好复杂易出错的关键服务，将简单易用的接口和性能高效、功能稳定的系统提供给用户。

ZooKeeper是以Fast Paxos算法为基础的，Paxos算法存在活锁的问题，即当有多个proposer交错提交时，有可能互相排斥导致没有一个proposer能提交成功，而FastPaxos做了一些优化，通过选举产生一个leader (领导者)，只有leader才能提交proposer，具体算法可见FastPaxos。因此，要想弄懂ZooKeeper首先得对Fast Paxos有所了解。
********
ZooKeeper的基本运转流程：
- 1、选举Leader。
- 2、同步数据。
- 3、选举Leader过程中算法有很多，但要达到的选举标准是一致的。
- 4、Leader要具有最高的执行ID，类似root权限。
- 5、集群中大多数的机器得到响应并接受选出的Leader。

## 二、安装
### 2.1 windows环境下单点[安装](https://www.cnblogs.com/riches/p/11199402.html)
```
1.下载
下载地址：https://archive.apache.org/dist/zookeeper/

2.解压

3.启动server
D:\zookeeper-3.4.9\bin>zkServer.cmd
启动成功：[myid:] - INFO  [main:NIOServerCnxnFactory@89] - binding to port 0.0.0.0/0.0.0.0:2181

4.客户端连接
D:\zookeeper-3.4.9\bin>zkCli.cmd
链接成功：
2020-11-26 17:48:58,528 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=localhost:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@277050dc
Welcome to ZooKeeper!
2020-11-26 17:48:59,291 [myid:] - INFO  [main-SendThread(0:0:0:0:0:0:0:1:2181):ClientCnxn$SendThread@1032] - Opening socket connection to server 0:0:0:0:0:0:0:1/0:0:0:0:0:0:0:1:2181. Will not attempt to authenticate using SASL (unknown error)
2020-11-26 17:48:59,293 [myid:] - INFO  [main-SendThread(0:0:0:0:0:0:0:1:2181):ClientCnxn$SendThread@876] - Socket connection established to 0:0:0:0:0:0:0:1/0:0:0:0:0:0:0:1:2181, initiating session
JLine support is enabled
2020-11-26 17:48:59,495 [myid:] - INFO  [main-SendThread(0:0:0:0:0:0:0:1:2181):ClientCnxn$SendThread@1299] - Session establishment complete on server 0:0:0:0:0:0:0:1/0:0:0:0:0:0:0:1:2181, sessionid = 0x17603f422800000, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
[zk: localhost:2181(CONNECTED) 0]

```

### 2.2 Linux环境下单点[安装](https://blog.csdn.net/shufangreal/article/details/108524408)
```
1.下载
apache-zookeeper-3.6.2-bin.tar.gz

2.解压
tar zxcf apache-zookeeper-3.6.2-bin.tar.gz

3.配置，一个节点不需要改配置内容
cd apache-zookeeper-3.6.2-bin/conf
cp zoo_sample.cfg zoo.cfg

4.启动
cd apache-zookeeper-3.6.2-bin/
./zkServer.sh start
启动成功
[root@localhost bin]# ./zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /opt/apache-zookeeper-3.6.2-bin/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[root@localhost bin]# 

5.链接
./zkCli.sh
链接成功：
2020-11-26 02:43:26,144 [myid:localhost:2181] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@1167] - Opening socket connection to server localhost/0:0:0:0:0:0:0:1:2181.
2020-11-26 02:43:26,144 [myid:localhost:2181] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@1169] - SASL config status: Will not attempt to authenticate using SASL (unknown error)
Welcome to ZooKeeper!
JLine support is enabled
2020-11-26 02:43:26,291 [myid:localhost:2181] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@999] - Socket connection established, initiating session, client: /0:0:0:0:0:0:0:1:44925, server: localhost/0:0:0:0:0:0:0:1:2181
2020-11-26 02:43:26,333 [myid:localhost:2181] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@1433] - Session establishment complete on server localhost/0:0:0:0:0:0:0:1:2181, session id = 0x100007a3a720000, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null

```

### 2.2 Linux环境下集群安装
[Apache-Zookeeper-3.6.2 的集群安装](https://blog.csdn.net/shufangreal/article/details/108524408)

