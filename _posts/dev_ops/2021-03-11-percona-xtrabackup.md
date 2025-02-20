---
layout: post
title: Mysql备份恢复方案
category: dev-ops
tags: [dev-ops]
---

Mysql备份恢复方案：mysqldump、percona-xtrabackup

## 参考资料
- percona-xtrabackup官方文档：https://docs.percona.com/percona-xtrabackup/2.4/index.html
- MySQL备份方案 https://blog.csdn.net/KW__jiaoq/article/details/121878337
- mysql备份的三种方案 https://blog.51cto.com/u_11027113/6255068 
- MySQL备份与恢复 https://blog.csdn.net/weixin_49343462/article/details/111603428

## 1.利用mysqldump工具对数据进行备份和还原-冷备

``` 
## 备份单库
$> /opt/mysql-5.7/bin/mysqldump -uroot -p123456 -h localhost --databases test >test.sql

## 备份所有库
$> /opt/mysql-5.7/bin/mysqldump -h 127.0.0.1 -P 3306 -uroot -p123456 -x --all-databases > alldb.sql

## 还原单库方式1
$> mysql -uroot -p123456 test < test.sql  # 指定test库还原

## 还原单库方式2
$>mysql -uroot -p123456
mysql> use test  # 执行source命令前需要先选择数据库。
MYSQL> source test.sql;

## 还原所有库
$> mysql -u root -p123456 < alldb.sql 

```

## 2.利用mysqldump工具对数据进行备份和还原-定时任务sh脚本每日备份

1. 编写备份脚本backup_mysql.sh  

``` 
#!/bin/bash
# 备份目录

BACKUP_DIR=/data/mysql_backup
if [ ! -d "$BACKUP_DIR" ]; then
mkdir -p "$BACKUP_DIR"
fi

# 数据库用户名
DB_USER=root
# host
HOST_IP=mysql_ip
# 数据库密码, 密码中有特殊字符如$，需要加'',否则shell脚本中密码无法正确识别
#DB_PASSWD='123$456'
DB_PASSWD=123456   
# 数据库名称
DB_NAME=--all-databases

# 备份文件名
BACKUP_FILE=mysql_$(date +%Y%m%d%H%M%S).sql

# 创建备份目录
mkdir -p $BACKUP_DIR

# 备份数据库
# mysqldump -h $HOST_IP -u$DB_USER -p$DB_PASSWD $DB_NAME > $BACKUP_DIR/$BACKUP_FILE
# mysqldump -h mysql_ip -uroot -p123456  --single-transaction --master-data=2 --all-databases > $BACKUP_DIR/$BACKUP_FILE  # 导出时保证事务一致，并且记录当前binlog和position
mysqldump -h mysql_ip -uroot -p123456 --all-databases > $BACKUP_DIR/$BACKUP_FILE

# 压缩备份文件
gzip $BACKUP_DIR/$BACKUP_FILE

# 输出备份完成信息
echo "MySQL backup completed: $BACKUP_FILE.gz"
```

2. 添加定时任务  

``` 
$chmod +x backup_mysql.sh

$ crontab -e，增加
0 2 * * * /bin/bash /data/mysql_backup/backup_mysql.sh
```

## 3.[Mysql使用percona-xtrabackup进行数据备份还原](https://zhuanlan.zhihu.com/p/323722709)

- percona-xtrabackup-24-2.4.7-1.el6.x86_64.rpm下载：https://www.percona.com/downloads
- libev依赖下载：https://rpmfind.net/linux/centos/8-stream/AppStream/x86_64/os/Packages/libev-4.24-6.el8.x86_64.rpm

参考文档：  
- 【xtrabackup实现mysql备份】：https://blog.csdn.net/qq_37119960/article/details/129413589
- MySQL第三方备份工具Percona XtraBackup https://blog.csdn.net/weixin_58131623/article/details/129015166
- 使用percona-xtrabackup备份MySQL数据 https://blog.csdn.net/qq_41941256/article/details/132364058
- 基于Percona XtraBackup 实现MySQL数据库全备&增量备份与恢复 https://blog.csdn.net/qq_28721869/article/details/118853884
- MySQL之Xtrabackup备份与恢复 https://blog.csdn.net/Richardlygo/article/details/100163556

### 3.1安装

``` 
直接执行安装命令，可能会提示缺少相关的依赖包，如 libev.so.4()(64bit)、perl-DBD-MySQL、perl-Digest-MD5等，可以先通过yum命令安装相关依赖
yum install cmake gcc gcc-c++ libaio libaio-devel automake autoconf bzr
yum -y install perl perl-devel libaio libaio-devel perl-Time-HiRes perl-DBD-MySQL perl-Digest-MD5

rpm -ivh libev-4.24-6.el8.x86_64.rpm
rpm -ivh percona-xtrabackup-24-2.4.7-1.el6.x86_64.rpm
```

### 3.2 全量备份/恢复
总结全库备份与恢复三步曲：  
- a.innobackupex全量备份，并指定备份目录路径；  
- b.在恢复前，需要使用--apply-log参数先进行合并数据文件，确保数据的一致性要求；  
- c.恢复时，直接使用--copy-back参数进行恢复，需要注意的是，在my.cnf中要指定数据文件目录的路径。  

### 3.3 增量备份/恢复
- （1）增量备份需要使用参数--incremental指定需要备份到哪个目录，使用incremental-dir指定全备目录；
- （2）进行数据备份时，需要使用参数--apply-log redo-only先合并全备数据目录数据，确保全备数据目录数据的一致性；
- （3）再将增量备份数据使用参数--incremental-dir合并到全备数据当中；
- （4）最后通过最后的全备数据进行恢复数据，注意，如果有多个增量备份，需要逐一合并到全备数据当中，再进行恢复

### 3.4.通过定时任务，每日执行备份
需求：通过 crontab -e 添加shell脚本，实现每日增量备份、每周全量备份。

## 问题记录  
1.mysql 如何线上添加从机器 不停机：https://jingyan.baidu.com/article/c275f6bae7d709a23d7567e8.html  
https://www.cnblogs.com/cheyunhua/p/9896167.html  

- single-transaction 选项和 lock-all-tables 选项是二选一的，前者是在导出开始时设置事务隔离状态并使用一致性快照开始事务,而后马上unlock tables，然后执行导出,导出过程不影响其它事务或业务连接，但只支持类似innodb多版本特性的引擎，因为必须保证即使导出期间其它操作(事务点t2)改变了数据，而导出时仍能取出导出开始的事务点t1时的数据。
- lock-all-tables则一开始就 FLUSH TABLES WITH READ LOCK; 加全局读锁，直到dump完毕。
- master-data=2时，备份会记录当前的binlog位置与pos，他是不停机做主从的关键，默认等于1，将dump起始（change master to）binlog点和pos值写到结果中，等于2是将change master to写到结果中并注释。
