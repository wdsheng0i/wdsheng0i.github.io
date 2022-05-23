---
layout: post
title: 大数据之CDH安装与部署
category: acide
tags: [acide]
---

## 参考资料
Cloudera Hadoop: 大数据之CDH安装与部署

- [官网文档](https://docs.cloudera.com/documentation/enterprise/6/6.0/topics/cdh_intro.html)
- [也许是你见过最详细的CDH6安装](https://www.bilibili.com/video/BV17k4y1k7w7?from=search&seid=12272889522580209950)
- [CDH6.2.0搭建（史上最全的安装教程）](https://blog.csdn.net/weixin_38201936/article/details/106006335)
- [小白快速掌握CDH安装](https://study.163.com/course/courseLearn.htm?courseId=1210098904#/learn/video?lessonId=1280947250&courseId=1210098904)
- 《Cloudera Hadoop大数据平台实战指南》_宋立桓
- 实战指南配套： https://pan.baidu.com/s/1-P7Go5gdJLim33_Iju1rfg#list/path=%2F    d1tk

https://archive.cloudera.com/cm6/ 

CDH优点：  
- 统一化的可视化界面、服务主机和集群实时监控
- 自动部署和配置，大数据组件（hadoop、hive、impala...）安装调优便捷
- 0停机维护（免费版本不具有弹性升级）
- 稳定性好：部分优化措施已经调整好
- 多用户管理

CDH应用场景：（节点少，服务器紧张，且使用大数据组件少 建议直接选择开源版本使用）
- 节点大于5个
- 服务器资源不紧张
- 免费版本不具有弹性升级，所以版本要求稳定
- 减轻运维工作量

## 1.基础环境准备（3台主机都做好基础环境设置）  

| ip 	|名称	|内存大小|  安装组件|
| ---- | ---- | ---- | ---- |
|192.168.145.128	|cdh1	|16G|  server + agent |
|192.168.145.129	|cdh2	|16G|  agent |
|192.168.145.130	|cdh3	|16G|  agent + mysql|

### ip设置    
```
//配置静态（固定）ip
1.虚拟机网卡设置：NAT

2.打开这个文件，修改里面的一些参数就行了。
/etc/sysconfig/network-scripts/ifcfg-ens33
BOOTPROTO="static"
//取值：vmware编辑-虚拟网络编辑器-NAT-配置
IPADDR=192.168.145.128
GATEWAY=192.168.145.2
DNS1=192.168.145.2

3.重启网卡服务：service network restart
```

### 主机名hostname设置（临时+永久）    
```
[root@chd1 hadoop]# hostname chd1
[root@chd1 hadoop]# vi /etc/hostname
chd1 
```

### hosts文件修改（ip和主机名的映射关系,3台主机都要添加）    
```
[root@chd1 hadoop]# vi /etc/hosts
ip1 hostname1
ip2 hostname2
ip3 hostname3
```

### 关闭防火墙（临时+永久）  
```
关闭防火墙 systemctl stop firewalld 
关闭开机启动项：systemctl disable firewalld
```

### ssh免密登录（3台主机互相做免密）    
```
[root@chd1 hadoop]# ssh-keygen -t rsa //一路回车
[root@chd1 hadoop]# cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

//验证
$ ssh chd1

//配置免密登录从节点
$ ssh-copy-id -i chd2
$ ssh-copy-id -i chd3
```

### jdk安装:  
```
//1.下载：https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html
上传jdk-8u171-linux-x64.tar.gz至目录/opt

//2.解压
tar zxvf  jdk1.7.0_80.tar.gz

//3.配置环境变量  
vi /etc/profile
//底部新增
export JAVA_HOME=/opt/jdk1.8.0_171
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 

//生效
source /etc/profile
``` 

### 克隆出129、130 （非克隆虚拟机不需要这一步）
```
1.vm128-右键-管理-克隆-当前状态-创建完整克隆

2.克隆完成后修改配置
ipa ddr 查看ip、mac地址
...
# link/ether 00:0c:29:b0:26:ab brd ff:ff:ff:ff:ff:ff
# inet 192.168.145.130/24 brd 192.168.145.255 scope global dynamic eno16777736

vi /etc/sysconfig/network-scripts/idcfg-eno16777736
HWADDR=00:0c:29:b0:26:ab
IPADDR=192.168.145.130

3.service network restart
``` 

### 集群节点之间时间同步
```
yum install -y ntpdate
ntpdate -u ntp.sjtu.edu.cn

// 建议把这个同步时间的操作添加到linux的crontab定时器中，每分钟执行一次
vi /etc/crontab
* * * * * root /usr/sbin/ntpdate -u ntp.sjtu.edu.cn
```

### SELINUX 关闭
```
vim /etc/selinux/config
SELINUX=disabled (修改)
```

### 卸载mariadb
```
rpm -qa|grep mariadb
yum -y remove mariadb-libs
```

### 修改系统参数
```
sysctl vm.swappiness=10
echo 'vm.swappiness=10' >> /etc/sysctl.conf
echo never > /sys/kernel/mm/transparent_hugepage/defrag
echo never > /sys/kernel/mm/transparent_hugepage/enabled
echo 'echo never > /sys/kernel/mm/transparent_hugepage/defrag'  >> /etc/rc.local
echo 'echo never > /sys/kernel/mm/transparent_hugepage/enabled'  >> /etc/rc.local
```

### 安装httpd
```
yum -y install httpd
service httpd start 
service  httpd status

//直接用ip地址登录，验证一下
```

### 安装第三方依赖包
```
yum -y install chkconfig 
```

## 2.mysql安装(数据库只在cdh3 上面进行安装)
```
官网下载：https://dev.mysql.com/downloads/mysql/  
1.将下载好的mysql-8.0.17-linux-glibc2.12-x86_64.tar.xz上传到usr/local/mysql目录下(如果没有该目录可以依次建文件夹)

2. .tar.xz解压是  tar -xvf mysql-8.0.17-linux-glibc2.12-x86_64.tar.xz
   .tar.gz解压是  tar -zvxf mysql-8.0.17-linux-glibc2.12-x86_64.tar.xz

// 对mysql进行安装
1.重命名解压文件：进入mysql文件夹执行命令：mv mysql-8.0.17-linux-glibc2.12-x86_64 mysql
移动重命名后文件内的所有文件：进入重名名的mysql文件夹执行命令:mv * usr/local/mysql 
将刚刚空了的mysql文件夹删除；最后的结果mysql可执行文件bin的全路径应该是usr/local/mysql/bin

2.为系统添加mysql 组和用户：groupadd mysql和useradd -r -g mysql mysql

3.进入 /usr/local/mysql 目录下，修改相关权限：chown -R mysql:mysql ./   //修改当前目录为mysql用户

4.如果你/etc下没有my.cnf文件, 新建一个my-defalut.cnf文件,将其复制到/etc/my.cnf
touch my-defalut.cnf //新建一个文件
chmod 755 my-defalut.cnf // 赋予权限
cp my-defalut.cnf /etc/my.cnf // 将文件复制到/etc/ 目录下，并更名为my.cnf 文件名

5.my.cnf 的信息如下
[mysqld]
#设置表名大小写不敏感
lower_case_table_names=1
#设置mysql安装目录
basedir=/usr/local/mysql
#设置mysql数据库的数据存放目录
datadir=/usr/local/mysql/data
port=3306
socket=/tmp/mysql.sock
#设置mysql的日志文件位置（这个配置文件先不要放开，不然会报找不到Mysql.log文件，等启动后再放开，在重启）
#log-error=/var/log/mysql.log
#注意了,小细节，这里的 $hostname 是linux的主机名。一般每个人主机名都是不一样的。
pid-file=/usr/local/mysql/data/$hostname.pid
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

6.mysql初始化操作,记录下临时密码,之后第一次登录的时候会用到。
bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
//运行完成会生成一个暂时的密码

7.为mysql配置环境变量。 vim /etc/profile // 打开profile文件在最后追加下面命令
export MYSQL_HOME
MYSQL_HOME=/usr/local/mysql
export PATH=$PATH:$MYSQL_HOME/lib:$MYSQL_HOME/bin

退出后让其立马生效命令：source /etc/profile

8.设置为开机自启动项，依次执行下面代码
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
chmod +x /etc/init.d/mysql  //添加可执行权限。
chkconfig --add mysql  // 注册启动服务
输入chkconfig --list //查看是否添加成功。
 
9.启动msyql，并登陆
service mysql start //开启服务器。

注意：如果启动报了pid找不到的错误，直接把my.cnf中pid-file=/usr/local/mysql/data/$hostname.pid删掉，在启动就可以了

mysql -uroot -p //登录进入mysql，然后提示输入密码。

10.输入初始化过程中生成的临时密码，然后回车就行。进入一下页面。

11.进入mysql后，修改密码。不然你什么也做不了。
alter user 'root'@'localhost' identified by 'your_password';
其中'your_password'是你设置的新密码

11.创建远程操作账户，进行远程访问的授权,并更新权限，依次执行以下代码：
create user 'root'@'%' identified with mysql_native_password by '123456';
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;

备注：如果创建远程账号的密码错了，修改方法点击这里https://blog.csdn.net/qq_36631900/article/details/103404515

12.本地用telnet连接测试下能否连接，命令是：telnet 你安装数据库的服务器ip 3306
如果直接进入全黑界面，这就代表远程访问成功，或者你也可以用navicat连接尝试
```

## 3.CDH安装
Cloudera Manger下载地址为： https://archive.cloudera.com/cm6/6.2.0/redhat7/yum/RPMS/x86_64/  
cdh6下载：https://archive.cloudera.com/cdh6/6.2.0/parcels/   

上述文件整理资料百度云下载地址为：    
链接: https://pan.baidu.com/s/1Dm5Elf9uQqn14BUbgU3AFQ 提取码: mws3 

### mysql：创建cloudera manager需要的数据库
```
create database cmf default character set='utf8';
create database amon default character set='utf8';
create database hive default character set='latin1';
create database oozie default character set='utf8';
create database hue default character set='utf8';
```

### 上传mysql-connector-java（三台主机都执行）
```
mkdir /usr/share/java
mysql-connector-java.jar上传到 /usr/share/java 目录下
``` 

### 安装Cloudera Manager
全部节点都安装daemons和agent
``` 
yum localinstall -y cloudera-manager-daemons-6.2.0-968826.el7.x86_64.rpm 
yum localinstall -y cloudera-manager-agent-6.2.0-968826.el7.x86_64.rpm 
```

cdh1 节点  安装server 
```
yum localinstall -y cloudera-manager-server-6.2.0-968826.el7.x86_64.rpm
```

### CDH6.2.0安装/启动
1.cdh1上传   
```
CDH-6.2.0-1.cdh6.2.0.p0.967373-el7.parcel*    到目录 /opt/cloudera/parcel-repo/ 下  
PHOENIX-5.0.0-cdh6.2.0.p0.1308267-el7.parcel* 到目录 /opt/cloudera/parcel-repo/ 下  
manifest.json                                 到目录 /opt/cloudera/parcel-repo/ 下  
PHOENIX-1.0.jar                               到目录 /opt/cloudera/csd/ 下
```  

2.校验：  加密值一致即包下载完整    
``` 
//执行命令，对比加密值
sha1sum CDH-6.2.0-1.cdh6.2.0.p0.967373-el7.parcel
cat CDH-6.2.0-1.cdh6.2.0.p0.967373-el7.parcel.sha

sha1sum PHOENIX-5.0.0-cdh6.2.0.p0.1308267-el7.parcel
cat PHOENIX-5.0.0-cdh6.2.0.p0.1308267-el7.parcel.sha
``` 

3.修改server配置文件（cdh1 server节点上修改server）
``` 
vi /etc/cloudera‐scm‐server/db.properties

com.cloudera.cmf.db.host=cdh3
com.cloudera.cmf.db.name=cmf
com.cloudera.cmf.db.user=root
com.cloudera.cmf.db.password=root 
com.cloudera.cmf.db.setupType=EXTERNAL
```

4.修改agent配置文件（所有节点）
```  
vi /etc/cloudera-scm-agent/config.ini
server_host=cdh1
```

5.启动
``` 
#在cdh1 上启动server
systemctl start cloudera-scm-server

#另开一个窗口，查看相关日志。有异常就解决异常 
tail -200f /var/log/cloudera-scm-server/cloudera-scm-server.log

# 在全部节点上启动agent
systemctl start cloudera-scm-agent

# 当在 cdh1上 netstat ‐tunlp | grep 7180 有内容时，说明我们可以访问web页面了 
```

问题：
``` 
启动失败：https://blog.csdn.net/a544258023/article/details/107856387
查看服务失败信息：service cloudera-scm-server status

在一个脚本文件中找到了些答案，他会去使用/usr/java下的jdk，所以解决办法执行以下两条命令即可：
mkdir -p /usr/java
ln -s /opt/module/jdk1.8.0_212 /usr/java/default
```

## 4.安装大数据组件
Cloudera Manager web: http://cdh1:7180/  
默认超管：admin/admin，超管登陆入之后可以根据需要新建"仅查看权限"的账号  

### 通过Cloudera Manager安装各种大数据组件  
组件较多，不在赘述，具体参考  [CDH6.2.0搭建（史上最全的安装教程）](https://blog.csdn.net/weixin_38201936/article/details/106006335)  
各组件安装步骤基本相同  

|服务类型|	说明|
| ---- | ---- |
|ADLS Connector	  |The ADLS Connector service provides key management for accessing ADLS Gen1 accounts and ADLS Gen2 containers from CDH services.|
|Accumulo	      |The Apache Accumulo sorted, distributed key/value store is a robust, scalable, high performance data storage and retrieval system. This service only works with releases meant to run on top of CDH6.|
|Flume	          |Flume 从几乎所有来源收集数据并将这些数据聚合到永久性存储（如 HDFS）中。|
|HBase	          |Apache HBase 提供对大型数据集的随机、实时的读/写访问权限（需要 HDFS 和 ZooKeeper）。|
|HDFS	          |Apache Hadoop 分布式文件系统 (HDFS) 是 Hadoop 应用程序使用的主要存储系统。HDFS 创建多个数据块副本并将它们分布在整个群集的计算主机上，以启用可靠且极其快速的计算功能。|
|Hive	          |Hive 是一种数据仓库系统，提供名为 HiveQL 的 SQL 类语言。|
|Hue	          |Hue 是与包括 Apache Hadoop 的 Cloudera Distribution 配合使用的图形用户界面(需要 HDFS、MapReduce 和 Hive)。|
|Impala	          |Impala 为存储在 HDFS 和 HBase 中的数据提供了一个实时 SQL 查询接口。Impala 需要 Hive 服务，并与 Hue 共享 Hive Metastore。|
|Isilon	          |EMC Isilon is a distributed filesystem.|
|Java KeyStore KMS	|The Hadoop Key Management Service with file-based Java KeyStore. Maintains a single copy of keys, using simple password-based protection. Requires CDH 6.0+. Not recommended for production use.|
|Kafka	  			|Apache Kafka is publish-subscribe messaging rethought as a distributed commit log.|
|Key-Value Store Indexer	|键/值 Store Indexer 侦听 HBase 中所含表内的数据变化，并使用 Solr 为其创建索引。|
|Kudu				|Kudu is a true column store for the Hadoop ecosystem.|
|Oozie				|Oozie 是群集中管理数据处理作业的工作流协调服务。|
|Phoenix			|Apache Phoenix is an open source, massively parallel, relational database engine supporting OLTP for Hadoop using Apache HBase as its backing store.
|S3 Connector	    |The S3 Connector Service securely provides a single set of AWS credentials to Impala and Hue. This enables Hue administrators to browse the S3 filesystem and define Impala tables backed by S3 data authorized to that AWS identity, and also enables Impala users to query S3-backed tables without directly providing AWS credentials, subject to having the proper permissions defined via Sentry. The S3 Connector only supports the S3A protocol.|
|Sentry				|Sentry 	服务存储身份验证政策元数据并为客户端提供对该元数据的并发安全访问。|
|Solr				|Solr 是一个分布式服务，用于编制存储在 HDFS 中的数据的索引并搜索这些数据。|
|Spark				|Apache Spark is an open source cluster computing system. This service runs Spark as an application on YARN.|
|Sqoop 1 Client		|Configuration and connector management for Sqoop 1.|
| YARN (MR2 Included)  		    	|	Apache Hadoop MapReduce 2.0 (MRv2) 或 YARN 是支持 MapReduce 应用程序的数据计算框架（需要 HDFS）。|
|ZooKeeper			|Apache ZooKeeper 是用于维护和同步配置数据的集中服务。|







