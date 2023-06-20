---
layout: post
title: clickhouse
category: dev-ops
tags: [dev-ops]
---

## 参考资料

##  clickhouse 安装
前提: 
- 计划安装clickhouse的机器需要设置 FQDN ,且均需要在 /etc/hosts 下添加其他节点的 FQDN
- zk集群部署

```
* 参考网址  
https://clickhouse.tech/  
https://github.com/ClickHouse
* 下载
https://github.com/ClickHouse/ClickHouse/releases/download/v20.11.3.3-stable/clickhouse-client-20.11.3.3-2.noarch.rpm
https://github.com/ClickHouse/ClickHouse/releases/download/v20.11.3.3-stable/clickhouse-common-static-20.11.3.3-2.x86_64.rpm
https://github.com/ClickHouse/ClickHouse/releases/download/v20.11.3.3-stable/clickhouse-common-static-dbg-20.11.3.3-2.x86_64.rpm
https://github.com/ClickHouse/ClickHouse/releases/download/v20.11.3.3-stable/clickhouse-server-20.11.3.3-2.noarch.rpm
https://github.com/ClickHouse/ClickHouse/releases/download/v20.11.3.3-stable/clickhouse-test-20.11.3.3-2.noarch.rpm
```

### zookeeper 集群安装和配置

前置要求
``` 
* zookeeper 版本 apache-zookeeper-3.6.2-bin.tar.gz
```
部署步骤
* 解压至  /opt/zookeeper/apache-zookeeper-3.6.2-bin
* 新建目录 /opt/zookeeper/zk_data/data   /opt/zookeeper/zk_data/log
* 配置 /opt/zookeeper/apache-zookeeper-3.6.2/conf/zoo.cfg
```
tickTime=2000
initLimit=30000
syncLimit=10
maxClientCnxns=2000
autopurge.snapRetainCount=10
autopurge.purgeInterval=1
preAllocSize=131072
snapCount=3000000

maxSessionTimeout=60000000
dataDir=/opt/zookeeper/zk_data/data
dataLogDir=/opt/zookeeper/zk_data/log
server.1={host1}:2888:3888
server.2={host2}:2888:3888
server.3={host3}:2888:3888
```

* 新建 文件/opt/zookeeper/zk_data/data/myid  值为 server.${num} 中的 num  
  `echo $num > /opt/zookeeper/zk_data/data/myid`
* 环境变量
```
export ZOOKEEPER_HOME=/opt/zookeeper/apache-zookeeper-3.6.2-bin  
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

* 依次启动  `zkServer.sh start`

### clickhouse部署步骤
* 依次安装,当缺少依赖时,需参考 `可能的依赖`
   ```
  rpm -ivh clickhouse-common-static-20.11.3.3-2.x86_64.rpm
  rpm -ivh clickhouse-common-static-dbg-20.11.3.3-2.x86_64.rpm
  rpm -ivh clickhouse-server-20.11.3.3-2.noarch.rpm
  rpm -ivh clickhouse-test-20.11.3.3-2.noarch.rpm
  rpm -ivh clickhouse-client-20.11.3.3-2.noarch.rpm
  ```
* 可能的依赖(视情况而定,无需全部安装)
    ```
      yum install libicu unixODB
      yum install perl-JSON-XS -y
      yum -y install autoconf
      yum install expect
    ```

* 集群配置
    ```
    cd /etc/clickhouse-server
    
    ```
1. vim config.xml
    ``` 
    <logger> 标签内为日志相关配置,现网根据实际情况和磁盘情况配置相应的日志级别和日志保存路径,最好使用容量较大的磁盘
    
    <interserver_http_host>${填 hostname -f 返回的值,不存在需建立}</interserver_http_host>
        
    <!--设置时区-->
    <timezone>Asia/Shanghai</timezone>
    
    <!--集群相关的外部配置文件,不存在则新建此文件-->
    <include_from>/etc/clickhouse-server/metrika.xml</include_from>
    
    <!--TCP端口,按需修改,此处修改为 24001-->
    <tcp_port>24001</tcp_port>
    <!--备份间数据交互端口-->
    <interserver_http_port>25001</interserver_http_port>
    <!--数据存储目录, 最好使用容量较大的磁盘,磁盘路径用户设置为 clickhouse -->
    <path>/var/lib/clickhouse/</path>
     <!--查询时临时数据文件存储目录,最好使用容量较大的磁盘, 磁盘路径用户设置为 clickhouse -->
    <tmp_path>/var/lib/clickhouse/tmp/</tmp_path>  
    <!-- clickhouse_remote_servers 来源  metrika.xml
    同时, remote_servers 标签内的集群配置可以删除
    --> 
    <remote_servers incl="clickhouse_remote_servers" />
   
    <!-- 访问地址 -->
    <listen_host>0.0.0.0</listen_host>
   更好的配置如下: 
    <listen_host>::</listen_host>
   
    <!--
     Path in ZooKeeper to queue with DDL queries
    ddl_101_102 可以替换或者不配置 '/ddl_101_102',
    此处使用 '/ddl_101_102' 因开发环境集群的机器ip为 x.x.x.101 和 x.x.x.102
     -->
    <path>/clickhouse/task_queue/ddl_101_102</path>
    <!-- 值0表示您可以删除所有表而不受任何限制。1 表示超过50G的表无法删除 -->
    <max_table_size_to_drop>0</max_table_size_to_drop>
    
   ```
2. vim /etc/clickhouse-server/metrika.xml
    ```
    <yandex>
        <!-- ck集群节点 -->
        <clickhouse_remote_servers>
            <!-- 1个分片名称 , 此名称较重要,可不修改 -->
            <perftest_2shards_2replicas>
                <shard>
                    <!-- 权重 -->
                    <weight>1</weight>
                    <internal_replication>true</internal_replication>
                    <replica>
                        <host>集群机器1 HOST host -f </host>
                        <!-- config.xml 的 tcp_port -->
                        <port>24001</port>
                    </replica>
                    <replica>
                        <host>集群机器2 HOST host -f </host>
                        <port>24001</port>
                    </replica>
                </shard>
            </perftest_2shards_2replicas>
        </clickhouse_remote_servers>
    
        <!--zookeeper相关配置-->
        <zookeeper-servers>
            <node index="1">
                <host>zk_host1</host>
                <port>2181</port>
            </node>
            <node index="2">
                <host>zk_host2</host>
                <port>2181</port>
            </node>
            <node index="3">
                <host>zk_host1</host>
                <port>2181</port>
            </node>
        </zookeeper-servers>
    
        <macros>
            <!-- 同一个shard中,分片id相同,此处是 集群机器1+集群机器2, 都可配置为 
            shard_${host1 ip 第四段}_${host2 ip 第四段}
            -->
            <shard>shard_101_102</shard>
            <!--当前节点主机名 , 每个主机的配置值不一样-->
            <replica>{取 host -f 返回值}</replica>
        </macros>
        <!-- 可不配置 -->
        <networks>
            <ip>::/0</ip>
        </networks>
    
        <!--压缩相关配置-->
        <clickhouse_compression>
            <case>
                <min_part_size>10000000000</min_part_size>
                <min_part_size_ratio>0.01</min_part_size_ratio>
                <!--压缩算法lz4压缩比zstd快, 更占磁盘-->
                <method>lz4</method>
            </case>
        </clickhouse_compression>
    </yandex>
    ```
   修改 /etc/clickhouse-server/metrika.xml 权限  
   `chown clickhouse:clickhouse /etc/clickhouse-server/metrika.xml`  
   3 user.xml   
   user.xml 中已经包含默认用户  default , 此处为方便管理, 增加 test 账号, 以便 使用 jdbc 连接  
   增加位置在  <users></users> 标签内, 与用户 default 的标签 <default></default> 平级。
   ```
      <users>
        <!-- 用户  test 的相关配置 -->
        <test>
            <!-- 使用默认值 -->
            <quota>default</quota>
            <!-- 使用默认值 -->
            <profile>default</profile>
            <networks incl="networks" replace="replace">
                <ip>::/0</ip>
            </networks>
            <!-- 密码可自由配置 -->
            <password>123456</password>
        </test>
      </users>
   ```
* 集群启动, 依次使用 `clickhouse-server start` 启动集群
* 其他命令
    ```
    停止集群单实例 clickhouse-server stop
    重启集群单实例 clickhouse-server restart
    ```
* 检验集群
    ``` 
   clickhouse-client -m --host 某集群端口 --port ${config.xml 的 tcp_port}
    使用    select * from system.clusters; 检查集群信息
    ```