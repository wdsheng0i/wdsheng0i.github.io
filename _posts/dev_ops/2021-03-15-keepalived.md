---
layout: post
title: Keepalived 原理与实战
category: dev-ops
tags: [dev-ops]
---

## 参考资料
- 官网地址：https://www.keepalived.org
- 下载地址：https://www.keepalived.org/download.html
- 文档地址：  
- Keepalived 原理与实战、双机主备、双主热备  
- [java架构直通车-第6周 LVS+Nginx实现高可用集群](https://class.imooc.com/sale/javaarchitect)

## 一、keepalived简介、安装
![](https://wdsheng0i.github.io/assets/images/2021/keepalived/ha.png)   
- Keepalived能够解决单点故障，实现HA高可用机制
- 只有当主节点宕机，备用节点采用启用；主节点重新可用时，备节点会自动退出(主节点会向备节点发送心跳)。  
- 构建有多个路由器(nginx)的master backup路由器组, 监测nginx状态
- 虚拟IP-VIP（virtual IP Address）:linux安装后，网卡下有两个ip，一个ip，一个vip
- 基于**VRRP**：虚拟路由冗余协议(Virtual Router Redundancy Protocol，简称VRRP)  

是由IETF提出的解决局域网中配置静态网关出现单点失效现象的路由协议。
VRRP广泛应用在边缘网络中，它的设计目标是支持特定情况下IP数据流量失败转移不会引起混乱，允许主机使用单路由器，以及即使在实际第一跳路由器使用失败的情形下仍能够维护路由器间的连通性

### 1.1 Keepalived在线安装
``` 
yum -y install Keepalived  
配置  vi /etc/keepalived/keepalived.conf
```

### 1.2 Keepalived离线安装 
1.下载[Keepalived](https://www.keepalived.org/download.html)
https://www.keepalived.org/download.html

2.通过ftp工具上传到linux中

3.解压  
```tar -zxvf keepalived-2.0.18.tar.gz```

4.解压后进入到解压出来的目录，看到会有 configure ，那么就可以做配置了（配置安装和nginx一模一样）
```
./configure --prefix=/usr/local/keepalived --sysconf=/etc
```

5.make & make install

6.问题：缺少libnl  
```yum -y install libnl libnl-devel```

## 二、Keepalived实现双机主备高可用部署架构
### 2.1 高可用集群架构 Keepalived 双机主备原理
- 主备节点硬件配置保持相同，才能有相同的抗压能力，保证高可用  

![](https://wdsheng0i.github.io/assets/images/2021/keepalived/kpl.png)    

![](https://wdsheng0i.github.io/assets/images/2021/keepalived/vip.png)  

### 2.2 Keepalived实现双机主备（两keepalived绑同一个vip）高可用-（配置 Keepalived-主） 
1.通过命令 vim keepalived.conf 打开配置文件  
```
global_defs { 
   # 路由id：当前安装keepalived的节点主机标识符，保证全局唯一 
   router_id keep_171 
}
vrrp_instance VI_1 { 
    # 表示状态是MASTER主机还是备用机BACKUP 
    state MASTER 
    # 该实例绑定的网卡 ，查看网卡ip addr
    interface ens33 
    # 保证主备节点一致即可 
    virtual_router_id 51 
    # 权重，master权重一般高于backup，如果有多个，那就是选举，谁的权重高，谁就当选 
    priority 100 
    # 主备之间同步检查时间间隔，单位秒 
    advert_int 2 
    # 认证权限密码，防止非法节点进入 
    authentication { 
        auth_type PASS 
        auth_pass 1111 
    }
    # 虚拟出来的ip，可以有多个（vip） ,# 注意：主备两台的vip都是一样的，绑定到同一个vip
    virtual_ipaddress { 
        192.168.1.161 
    } 
}
```  

2.启动keepalived    
```
./keepalived
```

### 2.3 把Keepalived 注册为系统服务 
1.拷贝文件
```
#进入目录
cd /opt/keepalived/etc

#复制文件
cp init.d/keepalived /etc/init.d/
cp sysconfig/keepalived /etc/sysconfig/

# 生效系统命令
systemctl daemon-reload
```

2.启动 Keepalived    
``` 
# 启动keepalived 
systemctl start keepalived.service 

# 停止keepalived 
systemctl stop keepalived.service 

# 重启keepalived 
systemctl restart keepalived.service
```

3.查看进程  
```
ps -ef|grep keepalived  
```

### 2.3 Keepalived实现双机主备（两keepalived绑同一个vip）高可用-(配置 Keepalived-备)
1.通过命令 vim keepalived.conf 打开配置文件    
```
global_defs { 
    router_id keep_172 
}
vrrp_instance VI_1 { 
    # 备用机设置为BACKUP 
    state BACKUP 
    # 该实例绑定的网卡 
    interface ens33 
    # 保证主备节点一致即可 
    virtual_router_id 51 
    # 权重低于MASTER 
    priority 80 
    # 主备之间同步检查时间间隔，单位秒 
    advert_int 2 
    # 认证权限密码，防止非法节点进入 
    authentication { 
        auth_type PASS 
        auth_pass 1111 
    }
    virtual_ipaddress { 
        # 注意：主备两台的vip都是一样的，绑定到同一个vip 
        192.168.1.161 
    } 
}

``` 

### 2.4 Keepalived配置Nginx自动重启，实现7x24不间断服务 
为了保证nginx自动重启，提供7x24的不间断服务，需要自行添加脚本使得keepalived对nginx进行检测。

1.增加Nginx重启检测脚本  
```
vim /etc/keepalived/check_nginx_alive_or_not.sh 
#!/bin/bash 
A=`ps -C nginx --no-header |wc -l` 
# 判断nginx是否宕机，如果宕机了，尝试重启 
if [ $A -eq 0 ];then 
    /usr/local/nginx/sbin/nginx 
    # 等待一小会再次检查nginx，如果没有启动成功，则停止keepalived，使其启动备用机 
    sleep 3 
    if [ `ps -C nginx --no-header |wc -l` -eq 0 ];
        then killall keepalived 
    fi 
fi
```

2.增加运行权限    
```
chmod +x /etc/keepalived/check_nginx_alive_or_not.sh
```

3.配置keepalived监听nginx脚本  
``` 
vrrp_script check_nginx_alive { 
    script "/etc/keepalived/check_nginx_alive_or_not.sh" 
    interval 2 # 每隔两秒运行上一行脚本 
    weight 10 # 如果脚本运行失败，则升级权重+10 
}
```

4.在 vrrp_instance 中新增监控的脚本  
``` 
track_script { 
    check_nginx_alive # 追踪 nginx 脚本 
}
```

5.重启Keepalived使得配置文件生效  
```
systemctl restart keepalived
```

## 三、Keepalived实现双主热备高可用部署架构
互为主备  
![](https://wdsheng0i.github.io/assets/images/2021/keepalived/2master.png)

### 3.1 配置实现keepalived双主热备 （2keepalived分别绑两个虚拟ip）
规则：以一个虚拟ip分组归为同一个路由  

**vip1主、vip2备节点配置：**
```
global_defs { 
    router_id keep_171 
}
vrrp_instance VI_1 { 
    state MASTER 
    interface ens33 
    virtual_router_id 51 
    priority 100 
    advert_int 1 
    authentication { 
        auth_type PASS 
        auth_pass 1111 
    }
    virtual_ipaddress { 
        192.168.1.161 
    } 
}
vrrp_instance VI_2 { 
    state BACKUP 
    interface ens33 
    virtual_router_id 52 
    priority 80 
    advert_int 1 
    authentication { 
        auth_type PASS 
        auth_pass 1111 
    }
    virtual_ipaddress { 
        192.168.1.162 
    } 
}
```

**vip2主、vip1备节点配置**
```
global_defs { 
    router_id keep_172 
}
vrrp_instance VI_1 { 
    state BACKUP 
    interface ens33 
    virtual_router_id 51 
    priority 80 
    advert_int 1 
    authentication { 
        auth_type PASS 

        auth_pass 1111 
    }
    virtual_ipaddress { 
        192.168.1.161 
    } 
}
vrrp_instance VI_2 { 
    state MASTER 
    interface ens33 
    virtual_router_id 52 
    priority 100 
    advert_int 1 
    authentication { 
        auth_type PASS 
        auth_pass 1111 
    }
    virtual_ipaddress { 
        192.168.1.162 
    } 
}
```

## 四、常用track_script配置
### 4.1 chk_nginx.sh 
```
# 1.keepalived.conf
! Configuration File for keepalived

global_defs {
    router_id LVS_nginx_110
    vrrp_mcast_group4 224.0.0.110
}

vrrp_script chk_script {
    script "/etc/keepalived/chk_nginx.sh"
    interval 15 # 间隔15秒执行一次
    timeout 10 # 10秒超时
    fall 3 # 失败3次才算失败
}

vrrp_instance VI_1 {
    interface ens192 # 网卡
    state BACKUP # 主从均设置为BACKUP
    priority 150 # 主从权重设置一致
    advert_int 1
    nopreempt
    virtual_router_id 110 #路由ID，可通过#tcpdump vrrp查看。
    garp_master_delay 1 #主从切换时间，单位为秒。

    authentication {
        auth_type PASS
        auth_pass 000000
    }
    track_interface {
       ens192
    }
    virtual_ipaddress {
        192.168.1.110  # VIP
    }
    virtual_ipaddress_excluded {
        xxxx:8c60:xxxx:2:0:1:129:xxxx/112
    }
    track_script {
        chk_script
    }
}
# 2.chk_nginx.sh 
#!/bin/bash
run=`ps -C nginx --no-header | wc -l`
if [ $run -eq 0 ]
then
exit 1
fi
```

### 4.2 chk_mysql.sh
``` 
# 1.keepalived.conf
同上

# 2.chk_mysql.sh
#!/bin/bash
debuglog_path='/etc/keepalived/debug.log'
debugrun_path='/etc/keepalived/debug.run'

echo '['`date +"%Y-%m-%d %X"`']>>>>>>>>' > $debugrun_path 2>&1
mysql -h localhost -ukeepalived -p'123456' -e 'select @@version' >> $debugrun_path 2>&1

if [ $? -eq 0 ]; then
 echo 0
 exit 0
fi

cat $debugrun_path >> $debuglog_path

echo 1
exit 1
```

### 4.3 chk_redis.sh
``` 
# 1.keepalived.conf
同上

# 2.chk_redis.sh
#!/bin/bash
debuglog_path='/etc/keepalived/debug.log'
debugrun_path='/etc/keepalived/debug.run'

redis_path=redis-cli
host=127.0.0.1
port=$(cat /etc/redis.conf |grep -v ^# | grep -v "^$" | grep 'port ' |awk '{print $2}')
pwd=$(cat /etc/redis.conf |grep -v ^# | grep -v "^$" | grep 'requirepass ' |awk '{print $2}')
pwd=${pwd//\"/}

echo '['`date +"%Y-%m-%d %X"`']>>>>>>>>' > $debugrun_path 2>&1
timeout 5s $redis_path -h $host -p $port -a $pwd info Replication|grep role:master >> $debugrun_path 2>&1

if [ $? -eq 0 ]; then
 echo 'master'
 exit 0
fi

timeout 5s $redis_path -h $host -p $port -a $pwd info Replication|grep role:slave >> $debugrun_path 2>&1

if [ $? -eq 0 ]; then
 echo 'slave'
 exit 1
fi

cat $debugrun_path >> $debuglog_path

echo 'error'
exit 1
```

### 4.4 chk_k8s_master.sh
``` 
# 1.keepalived.conf
同上

# 2.chk_k8s_master.sh
#!/bin/bash
result=$(curl -k -s https://localhost:6443/ping)

if [[ ${result} == "pong" ]]; then
    exit 0
else
    exit 1
fi
```

## 云服务的DNS解析配置与负载均衡
用户请求经dns轮询发送给虚拟ip进行处理   

## 问题记录