---
layout: post
title: LVS + keepalived + Nginx实现负载均衡、高可用集群
category: arch
tags: [arch]
---

LVS + keepalived + Nginx实现负载均衡、高可用集群  

## 参考资料
[java架构直通车-第6周 LVS+keepalived+Nginx实现高可用集群](https://class.imooc.com/sale/javaarchitect)  
keepalived就是为lvs设计的，结合使用，通过keepalived可以配置lvs和nginx关系：实现主备高可用、负载均衡算法、健康检查、持久化链接

## 1 LVS(Linux Virtual Server)负载均衡器  
推荐: https://blog.51cto.com/13958408/2312052?source=dra

单个nginx往往是不够的，因为并发量还是有限，所以很多企业会采用LVS，LVS是四层负载，LVS涉及到NAT|TUN|DR这三种模式。    
需要注意，当keepalived以后，nginx作为lvs的集群，就无需和keepalived结合了。   
作为架构师，对lvs集群的负载算法有一定的了解即可，因为你要和运维人员进行有效沟通；    
作为运维, 是要深入钻研LVS了，一个项目如果发展到使用到LVS了，那么业务量是十分巨大的，需要有专门的运维团队来负责网络架构的。

- LVS是 Linux Virtual Server 的简称，也就是Linux虚拟服务器。  
- LVS是一个由章文嵩博士发起的一个开源项目，它的官方网是 http://www.linuxvirtualserver.org   
- LVS（核心ipvs） 已经是 Linux 内核标准的一部分。    
- 使用 LVS 可以达到的技术目标是：通过 LVS 达到的负载均衡技术和 Linux 操作系统实现一个高性能高可用的 Linux 服务器集群，它具有良好的可靠性、可扩展性和可操作性。从而以低廉的成本实现最优的性能。    
- LVS 是一个实现负载均衡集群的开源软件项目，LVS架构从逻辑上可分为调度层、Server集群层和共享存储。  
![](https://wdsheng0i.github.io/assets/images/2021/lvs/lvs.png)   

## 2 为什么要使用 LVS + Nginx？ 
- LVS基于四层，工作效率高
- 单个Nginx承受不了，需要集群
- LVS充当Nginx集群的调度者，负载均衡器
- Nginx接受请求来回，LVS可以只接受不响应
 

## 3 LVS的三种模式  
![NAT-基于网络地址转换](https://wdsheng0i.github.io/assets/images/2021/lvs/nat.png)  
![TUN-ip隧道](https://wdsheng0i.github.io/assets/images/2021/lvs/tun.png)
![DR-direact router](https://wdsheng0i.github.io/assets/images/2021/lvs/dr.png)  

## 4-5 搭建LVS-DR模式- 配置LVS节点与ipvsadm安装 
![](https://wdsheng0i.github.io/assets/images/2021/lvs/dip.png)   
1.服务器与ip规划：
```
LVS - 1台  
    VIP（虚拟IP）：192.168.1.150  
    DIP（转发者IP/内网IP）：192.168.1.151  
Nginx - 2台（RealServer）  
    RIP（真实IP/内网IP）：192.168.1.171  
    RIP（真实IP/内网IP）：192.168.1.172
```  

2.所有计算机节点关闭网络配置管理器，因为有可能会和网络接口冲突：
```
systemctl stop NetworkManager 
systemctl disable NetworkManager
```

3.进入到网卡配置目录，找到ens33：    

4.拷贝并且创建子接口：  
```cp ifcfg-ens33 ifcfg-ens33:1 * 注：`数字1`为别名，可以任取其他数字都行```   

5.修改子接口配置： vim ifcfg-ens33:1     

6.配置参考如下：    
```
BOOTPROTO="static"
DEVICE="ens33:1"
ONBOOT="yes"
IPADDR=192.168.1.150
NETMASK=255.255.255.0
```
7.安装ipvsadm 
```yum install ipvsadm ```

8.使用ipvsadm
```ipvsadm -Ln```

## 6-7 搭建LVS-DR模式- 为两台RealServer配置虚拟IP 
构建虚拟ip，仅仅用于返回用户数据  
1.进入到网卡配置目录，找到lo（本地环回接口，用户构建虚拟网络子接口），拷贝一份新的随后进行修改：
```
cd /etc/sysconf/network-scripts/
cp ifcfg-lo ifcfg-lo:1 * 注：`数字1`为别名，可以任取其他数字都行
``` 
2.修改内容如下：
```
DEVICE="lo:1"
IPADDR=192.168.1.150
NETMASK=255.255.255.255
NETWORK=127.0.0.0
BROADCAST=127.255.255.255
ONBOOT="yes"
NAME=loopback
```
3.重启网络服务  
```service network restart```

4.重启后通过ip addr 查看如下，表示ok：

## 8-9 搭建LVS-DR模式- 为两台RealServer配置arp 
**ARP响应级别与通告行为 的概念**   
1.arp-ignore：ARP响应级别（处理请求）
- 0：只要本机配置了ip，就能响应请求
- 1：请求的目标地址到达对应的网络接口，才会响应请求

2.arp-announce：ARP通告行为（返回响应）
- 0：本机上任何网络接口都向外通告，所有的网卡都能接受到通告
- 1：尽可能避免本网卡与不匹配的目标进行通告
- 2：只在本网卡通告

**配置ARP**   
1.打开sysctl.conf:    
```vim /etc/sysctl.conf```   

2.配置 所有网卡 、 默认网卡 以及 虚拟网卡 的arp响应级别和通告行为，分别对应： all ， default ， lo ：   
```
# configration for lvs 
net.ipv4.conf.all.arp_ignore = 1 
net.ipv4.conf.default.arp_ignore = 1 
net.ipv4.conf.lo.arp_ignore = 1 
net.ipv4.conf.all.arp_announce = 2 
net.ipv4.conf.default.arp_announce = 2 
net.ipv4.conf.lo.arp_announce = 2 
```

3.刷新配置文件：  
```sysctl -p```  

4.增加一个网关，用于接收数据报文，当有请求到本机后，会交给lo去处理：   
```route add -host 192.168.1.150 dev lo:1```     

5.查看
```route -n```  

6.加到开机自启  
``echo "route add -host 192.168.1.150 dev lo:1" >> /etc/rc.local``  

## 10-11 搭建LVS-DR模式- 使用ipvsadm配置集群规则  
1.创建LVS节点，用户访问的集群调度者  
```
ipvsadm -A -t 192.168.1.150:80 -s rr -p 5
-A：添加集群
-t：tcp协议
ip地址：设定集群的访问ip，也就是LVS的虚拟ip
-s：设置负载均衡的算法，rr表示轮询
-p：设置连接持久化的时间
```

2.创建2台RS真实服务器  
``` 
ipvsadm -a -t 192.168.1.150:80 -r 192.168.1.171:80 -g ipvsadm -a -t 192.168.1.150:80 -r 192.168.1.172:80 -g
-a：添加真实服务器
-t：tcp协议
-r：真实服务器的ip地址
-g：设定DR模式
```

3.保存到规则库，否则重启失效  
```ipvsadm -S ```

4.检查集群  
``` 
查看集群列表
ipvsadm -Ln
查看集群状态
ipvsadm -Ln --stats
``` 

5.其他命令：    
``` 
# 重启ipvsadm，重启后需要重新配置 
service ipvsadm restart 
# 查看持久化连接 
ipvsadm -Ln --persistent-conn 
# 查看连接请求过期时间以及请求源ip和目标ip 
ipvsadm -Lnc 
#设置tcp tcpfin udp 的过期时间（一般保持默认） 
ipvsadm --set 1 1 1 
# 查看过期时间 
ipvsadm -Ln --timeout
``` 

6.更详细的帮助文档：  
``` 
ipvsadm -h 
man ipvsadm
```

## 12 搭建LVS-DR模式- 验证DR模式，探讨LVS的持久化机制 
[lvs的DR模式详细部署](https://www.linkops.cn/sm/552.html)

## 13 搭建Keepalived+Lvs+Nginx高可用集群负载均衡 - 配置Master  
![](https://wdsheng0i.github.io/assets/images/2021/lvs/lvs-kp.png)  
![](https://wdsheng0i.github.io/assets/images/2021/lvs/lvs-kp-2.png)
1.前置准备：
```
服务器与ip规划：
LVS - 1台  
    VIP（虚拟IP）：192.168.1.150  
    DIP（转发者IP/内网IP）：192.168.1.151  
Nginx - 2台（RealServer）  
    RIP（真实IP/内网IP）：192.168.1.171  
    RIP（真实IP/内网IP）：192.168.1.172

配置LVS节点

为两台RealServer配置虚拟IP 

安装keepalived、ipvsadm    

switch绑定VIP域名：192.168.1.150  www.lvs.com

```  
2.keepalived.conf配置VIP、RIP节点信息  
```
global_defs { 
    router_id lvs_151
}
vrrp_instance VI_1 { 
    state MASTER 
    interface ens33 
    virtual_router_id 41 
    priority 100 
    advert_int 1 
    authentication { 
        auth_type PASS 
        auth_pass 1111 
    }
    virtual_ipaddress { 
        192.168.1.150 
    } 
}
#定义对外提供服务的LVS的VIP(集群地址访问的ip)以及port
virtual_server 192.168.1.150 80 {
    delay_loop 6 # 设置健康检查时间，单位是秒
    lb_algo rr # 设置负载调度的算法为轮询
    lb_kind DR # 设置LVS实现负载的机制
    #persistence_timeout 5 #设置会话持久化时间，添加后就持久化
    protocol TCP #协议

    ## 负载均衡的真实服务器，即nginx节点的真实IP地址
    real_server 192.168.1.171 80 {  # 指定real server1的IP地址
        weight 1   # 配置节点权值，数字越大权重越高
        #对后端节点进行健康检测
        TCP_CHECK { 
            connect_timeout 3 #超时时间
            nb_get_retry 3 #重试次数
            delay_before_retry 3 #多长时间重试
            connect_port 80
        }
    }
    real_server 192.168.1.172 80 {
        weight 1
        TCP_CHECK {
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 3
            connect_port 80
        }
     }
}
```
3.清理现有负载均衡规则  
```
ipvsadm -C

ipvsadm -Ln
```

4.重启keepalived 检查集群信息   
```
systemctl restart keepalived 

ipvsadm -Ln
```  

## 14 搭建Keepalived+Lvs+Nginx高可用集群负载均衡 - 配置Backup 
1.前置准备：
```
安装keepalived、ipvsadm  
配置/etc/keepalived/keepalived.conf
```  
2.配置  
```
global_defs { 
    router_id lvs_152
}
vrrp_instance VI_1 { 
    state BACKUP 
    interface ens33 
    virtual_router_id 41 
    priority 50
    advert_int 1 
    authentication { 
        auth_type PASS 
        auth_pass 1111 
    }
    virtual_ipaddress { 
        192.168.1.150 
    } 
}
#定义对外提供服务的LVS的VIP(集群地址访问的ip)以及port
virtual_server 192.168.1.150 80 {
    delay_loop 6 # 设置健康检查时间，单位是秒
    lb_algo rr # 设置负载调度的算法为轮询
    lb_kind DR # 设置LVS实现负载的机制
    #persistence_timeout 5 #设置会话持久化时间，添加后就持久化
    protocol TCP #协议

    ## 负载均衡的真实服务器，即nginx节点的真实IP地址
    real_server 192.168.1.171 80 {  # 指定real server1的IP地址
        weight 1   # 配置节点权值，数字越大权重越高
        #对后端节点进行健康检测
        TCP_CHECK { 
            connect_timeout 3 #超时时间
            nb_get_retry 3 #重试次数
            delay_before_retry 3 #多长时间重试
            connect_port 80
        }
    }
    real_server 192.168.1.172 80 {
        weight 1
        TCP_CHECK {
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 3
            connect_port 80
        }
     }
}
```
3.清理现有负载均衡规则  
```
ipvsadm -C

ipvsadm -Ln
```

4.重启keepalived 检查集群信息   
```
systemctl restart keepalived 

ipvsadm -Ln
``` 

5.验证
``` 
1.浏览器访问 www.lvs.com, 轮序171  172

2.停掉master keepalived，访问www.lvs.com正常，轮序171  172，ip addr 显示backup节点

3.恢复master keepalived，访问www.lvs.com正常，轮序171  172，ip addr 显示master节点

4.停掉一个real server Nginx服务， 访问www.lvs.com正常，仅172提供服务

5.恢复real server Nginx服务， 访问www.lvs.com正常，轮序171  172，ip addr 显示master节点

```

## 15 附：LVS的负载均衡算法
静态算法：根据LVS本身自由的固定的算法分发用户请求。  
- 1.轮询（Round Robin 简写’rr’）：轮询算法假设所有的服务器处理请求的能力都一样的，调度器会把所有的请求平均分配给每个真实服务器。（同Nginx的轮询）
- 2.加权轮询（Weight Round Robin 简写’wrr’）：安装权重比例分配用户请求。权重越高，被分配到处理的请求越多。（同Nginx的权重）
- 3.源地址散列（Source Hash 简写’sh’）：同一个用户ip的请求，会由同一个RS来处理。（同Nginx的ip_hash） 
- 4.目标地址散列（Destination Hash 简写’dh’）：根据url的不同，请求到不同的RS。（同Nginx的url_hash） 

动态算法：会根据流量的不同，或者服务器的压力不同来分配用户请求，这是动态计算的。  
- 1.最小连接数（Least Connections 简写’lc’）：把新的连接请求分配到当前连接数最小的服务器。
- 2.加权最少连接数（Weight Least Connections 简写’wlc’）：服务器的处理性能用数值来代表，权重越大处理的请求越多。Real Server 有可能会存在性能上
动态获取不同服务器的负载状况，把请求分发到性能好并且比较空闲的服务器。
- 3.最短期望延迟（Shortest Expected Delay 简写’sed’）：特殊的wlc算法。举例阐述，假设有ABC三台服务器，权重分别为1、2、3 。如果使用wlc算法的话
求进来，它可能会分给ABC中的任意一个。使用sed算法后会进行如下运算：  
A：（1+1）/1=2  
B：（1+2）/2=3/2  
C：（1+3）/3=4/3  
最终结果，会把这个请求交给得出运算结果最小的服务器。
- 4.最少队列调度（Never Queue 简写’nq’）：永不使用队列。如果有Real Server的连接数等于0，则直接把这个请求分配过去，不需要在排队等待运算了（s 

总结： LVS在实际使用过程中，负载均衡算法用的较多的分别为wlc或wrr，简单易用。


参考文献：[官方文档](http://www.linuxvirtualserver.org/zh/lvs4.html)