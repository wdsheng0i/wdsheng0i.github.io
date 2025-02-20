---
layout: post
title: Linux基础操作命令
category: operate-system
tags: [operate-system]
---

Linux基础操作命令

## 参考资料
- [鸟哥私房菜-基础学习篇]
- [鸟哥私房菜-服务器架设篇]
- [Linux就该这么学]

## 一、装机
[curl,apt-get,wget,yum的区别](https://blog.csdn.net/xcliang9418/article/details/122081142)   

一般来说著名的linux系统基本上分两大类：  
- RedHat系列：Redhat、Centos、Fedora等  
- Debian系列：Debian、Ubuntu等  

### 1.1 做raid
[图文并茂 RAID 技术全解 – RAID0、RAID1、RAID5、RAID100](https://mp.weixin.qq.com/s/XgHz65Fe5WogTxBp0kqs7w)

### 1.2 ssh跳转
```
ssh -p 22 root@192.168.0.1
```

### 1.3 配ip：
1.虚拟机装centos7后，ifconfig无ip地址  
1.1 获取动态ip
```
修改/etc/sysconfig/network-scripts/ifcfg-eno16777736
ONBOOT=no, 将no  改为yes
service network restart
或 systemctl restart network

service NetworkManager restart
systemctl restart NetworkManager

//linux 已修改ip地址（ONBOOT=yes）,但是不显示ip地址??
1.因为你的（VMware DHCP Service）这个服务没有开，进入计算机管理–》服务和应用程序–》服务 找到VMware DHCP Service打开就行了

2.控制面板\网络和 Internet\网络连接
VMware Network Adapter 启用
```

1.2 配置静态（固定）ip
```
1.虚拟机网卡设置：NAT

2.打开这个文件，修改里面的一些参数就行了。
/etc/sysconfig/network-scripts/ifcfg-ens33
BOOTPROTO="static"
//取值：vmware编辑-虚拟网络编辑器-NAT-配置
IPADDR=192.168.182.100
GATEWAY=192.168.182.2
DNS1=192.168.182.2

3.重启网卡服务：service network restart
或 systemctl restart network
或 service NetworkManager restart
```

2.windows无法ping通服务器
```
1.检查防火墙，
2.window虚拟网卡是否启用
```

3.查看出口ip（外网ip）
```
windows：cmd
curl cip.cc

centos:
curl cip.cc
curl -4s https://ipinfo.io

问题：curl: (6) Could not resolve host: cip.cc; 未知的错误
// 原因：外网使用正常，证明提供服务未出问题。内部访问报错’dns找不到这个网址‘，可能是某dns服务器或dns解析出现了问题。
//修改或添加dns(dns服务器地址可网上查询)
vi /etc/resolv.conf
nameserver 114.114.114.114
nameserver 223.5.5.5
//然后重启网卡
service network restart
```

### 1.4hostname设置
```
查看 hostname
临时设置 hostname bigdata01
永久设置 vi /etc/hostname，填写 bigdata01，重启生效
```

### 1.5 hosts文件修改
```
vi /etc/hosts
ip1 hostname1
ip2 hostname2
ip3 hostname3
```

### 1.6 修改[ssh端口](https://blog.csdn.net/qq_41736266/article/details/128486125)
```
sudo vim /etc/ssh/sshd_config
service sshd restart
或者
sudo systectl restart ssh

【注意】：
1、系统默认的ssh端口为22。如果要修改，则直接编辑22端口并将前面的“#”注释符要去掉，然后保存重启ssh服务或者重启主机即可。
2、如果系统上有设置防火墙，则建议修改增加防火墙规则，在终端输入`sudo firewall-cmd --permanent --zone=public --add-port=1222/tcp`
命令后回车，再输入当前登录用户的密码，回车，来修改防火墙规则；更改规则后，再输入`sudo firewall-cmd reload`命令重启防火墙让规则生效。或者在终端输入`sudo iptables -F`命令直接关闭防火墙即可。
```

### 1.7 防火墙iptables
netfilter/iptables是unix/Linux(2.4版本内核后)自带的一款优秀且免费的基于包过滤的防火墙工具。  
netfilter组件也称为内核空间（kernelspace），是内核的一部分，由一些信息包过滤表组成，这些表包含内核用来控制信息包过滤处理的规则集。  
iptables是一种组件工具，也称为用户空间（userspace），它使插入、修改和除去信息包过滤表中的规则变得  

- Linux防火墙iptables详解 https://www.cnblogs.com/The-day-of-the-wind/p/9300635.html
- Linux防火墙之——iptables和firewalld：https://blog.csdn.net/Zhaohui_Zhang/article/details/126090994
- 未安装iptables-service怎样修改iptables规则 https://blog.csdn.net/m0_59388826/article/details/135764103
- centos iptables 生效 centos7 iptables配置文件 https://blog.51cto.com/u_13303/11170984
``` 
- 1） 重启后生效
开启： chkconfig iptables on
关闭： chkconfig iptables off

- 2）即时生效，重启后失效
开启： service iptables start
关闭： service iptables stop

- 3）查看防火墙状态
service iptables status
关闭：iptables: Firewall is not running.

- 4）防火墙开放端口：

- 5）centos7防火墙：Centos7中的防火墙调整为firewalld
关闭防火墙 systemctl stop firewalld
查看 systemctl status firewalld
开启 systemctl start firewalld
关闭开机启动项：systemctl disable firewalld

执行一下命令：
systemctl stop firewalld
systemctl mask firewalld
sudo systemctl stop firewalld  //管理员授权命令

并且安装：iptables-services：
yum install iptables-services
设置开机启动：systemctl enable iptable
```

### 1.8 磁盘分区、格式化、挂载mount
[lsblk、fdisk、df -h、mount](https://blog.csdn.net/m0_54108654/article/details/126601804)
```
## 首先需要新增磁盘sdb，新增的硬件在dev下
lsblk 查看磁盘是否添加成功sdb

## 创建待挂载的目录
mkdir data

## 查看当前磁盘、分区信息
lsblk -f

## 对dev下的sdb盘进行分区
fdisk  /dev/sdb
m 查看命令帮助，n新增分区，p主分区，1，默认 默认 ，w写入并保存

## 格式化分区
mkfs -t ext4 /dev/sdb1

## 挂载分区与目录
mount /dev/sdb1 /data
```

### 1.9 虚拟机克隆
1.管理-克隆  

2.克隆完成后修改配置  
```
ipa ddr 查看ip、mac地址
...
# link/ether 00:0c:29:b0:26:ab brd ff:ff:ff:ff:ff:ff
# inet 192.168.145.130/24 brd 192.168.145.255 scope global dynamic eno16777736

vi /etc/sysconfig/network-scripts/idcfg-eno16777736
HWADDR=00:0c:29:b0:26:ab
IPADDR=192.168.145.130
```

3.service network restart

操作系统读磁盘，一次io读一页数据，8扇区*512b=4kb

## 二、目录规范
- /opt：基础环境、基础软件安装目录
- /usr：用户软件安装目录
- /home：用户目录
- /etc：配置文件目录
- /data: 自建用户数据目录

## 三、软件安装
- 1.在线安装：
  - curl https://xx/*install.sh 
  - yum install -y xxx
  - wget xxx.rpm下载后, 安装rpm -ivh xxx.rpm
- 2.离线安装：下载.tar、tar.gz，解压，修改配置、启动服务、注册系统服务
- 3.docker安装，注意挂载数据卷，持久化conf和data，映射端口
- 4.k8s安装，yml编写、镜像、命名空间，注意持久化conf和data、服务暴露

### 安装方式
`方式1，在线安装：yum install gcc`  
`方式2，离线安装：wget xxx.rpm ,  rpm -ivh vsftpd.rpm   --https://centos.pkgs.org/`

### 查看是否安装
`rpm -qa |grep yum `  

### 替换yum源
[使用国内Yum源](https://blog.csdn.net/nklinsirui/article/details/80146083)  

`/etc/yum.repos.d/CentOS-Base.repo`  

### 下载 wget  
`wget http://dlcdn.apache.org/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz`

### 安装 git
`yum install git -y`

### 安装 wget
`yum -y install wget`

### 安装 vim
`yum install vim -y`

### 安装 telnet
`yum install telnet`

### 安装 ifconfig
`yum install net-tools`  
`yum install ifconfig`

### 安装zip解压缩工具
` yum install unzip`

### 下载工具lrzsz
```
yum install lrzsz
```

下载命令
```
sz filename
```

上传命令
``` 
rz  
```

### 安装gcc
`yum install gcc`  

### 安装配置jdk
http://jingyan.baidu.com/article/e4d08ffdabb0710fd2f60de9.html
http://www.linuxidc.com/Linux/2016-11/136958.htm
```
1.tar zxvf  jdk1.7.0_80.tar.gz  //解压

2.配置环境变量
export JAVA_HOME=/home/jdk1.7.0_80
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
或者在 etc/profile中添加后执行source etc/profile  //对所有用户有效

3.执行
方法1：
让/etc/profile文件修改后立即生效 ,可以使用如下命令:
# .  /etc/profile
注意: . 和 /etc/profile 有空格
方法2：
让/etc/profile文件修改后立即生效 ,可以使用如下命令:
# source /etc/profile
```

方式2：
```
查看java JDK列表
yum -y list java*

通过yum安装JDK下载 java-1.8.0 可以选择自己需要的版本！！！
yum -y install java-1.8.0-openjdk*

等待安装完成
通过yum默认安装的路径为/usr/lib/jvm/java-版本号（如/usr/lib/jvm/java-1.8.0）
```

方式3：  
``` 
下载jdk-8u281-linux-x64.rpm  

使用rpm -ivh  jdk-8u281-linux-x64.rpm命令安装jdk。等待安装成功，输入java -version查看确认安装完毕。
rpm -ivh jdk-8u181-linux-x64.rpm 
```

### 安装配置tomcat
```
1.下载，移动，解压
sudo mv apache-tomcat-7.0.81.tar.gz /usr
sudo tar zxvf ./apache-tomcat-7.0.81.tar.gz

2.配置start.sh、shutdown.sh\    也可以不配置
#添加jdk和jre环境变量
JAVA_HOME=/home/jdk1.7.0_80
JRE_HOME=$JAVA_HOME/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME
CLASSPATH=.:$JRE_HOME/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
TOMCAT_HOME=/home/apache-tomcat-7.0.81

3.运行参数配置：catalina.sh   /catalina.bat
JAVA_OPTS="-Xms1024m -Xmx1024m -Xss1024k -XX:PermSize=128m -XX:MaxPermSize=256m"

4.三个端口都要改：server.xml
```

### 安装maven
https://blog.csdn.net/ameken/article/details/122368869
```
下载：
wget http://dlcdn.apache.org/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz

解压：
tar -xvf apache-maven-3.8.4-bin.tar.gz

改名：
mv apache-maven-3.8.4/ maven

配置环境变量
vi /etc/profile

export MAVEN_HOME=/home/maven
export PATH=$MAVEN_HOME/bin:$PATH

source /etc/profile

验证
mvn -v

修改目录下的 maven/conf/settings.xml

<mirrors>
<mirror>
<id>alimaven</id>
<name>aliyun maven</name>
<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
<mirrorOf>central</mirrorOf>
</mirror>
</mirrors>
```

### 装nodejs npm
https://blog.csdn.net/self321/article/details/116746460
```
1、从官网下下载最新的nodejs，https://nodejs.org/en/download/
历史版本可从https://nodejs.org/dist/下载

2、通过ftp工具上传到linux服务，解压安装包
tar -xvf node-v10.16.0-linux-x64.tar.xz

3、移动并改名文件夹（不改名也行）
cd /usr/local/
mv /var/ftp/pub/node-v10.16.0-linux-64 . //后面的.表示移动到当前目录
mv node-v10.16.0.0-linux-64/ nodejs

4、让npm和node命令全局生效  软链接方式（推荐）
ln -s /usr/local/nodejs/bin/npm /usr/local/bin/
ln -s /usr/local/nodejs/bin/node /usr/local/bin/

5、查看nodejs是否安装成功
node -v
npm -v
```

### whereis查找
```
whereis nginx  //命令只能用于程序名的搜索  
```

### 全局搜索：find  
```find / -name *minio* ``` 

## 四、系统、硬件监控命令
### 查看内存 free -m
```
free -m

可用：available = free + buffer/cache - 不可被回收内存(共享内存段、tmpfs、ramfs等)
```

### 查看磁盘 lsblk 、df -h
```
lsblk  
df -h
```

### 查看进程 top
```
top

ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%cpu | head
该命令可以列出当前CPU使用率最高的前几个进程，ps -eo pid,ppid,cmd,%cpu,%mem表示显示进程的PID、PPID、命令行、CPU使用率、内存使用率等信息，--sort=-%cpu表示按照CPU使用率的大小进行降序排列，head表示只显示前几个结果
```

### 查看 内存 cpu 进程 io 使用情况
```
vmstat
```

### 网络带宽测试工具speedtest-cli
``` 
yum install speedtest-cli
speedtest-cli
```

### du -hd1查看当前目录下各个文件及目录占用空间大小
```
du -sh *
```

```
du -lh --max-depth=1
du -hd1
```

### 主机ip网络是否连通
```
ping ip
```

### telnet tcp端口是否通
```
telnet ip port
```

### udp端口是否通
``` 
使用netcat工具测试UDP端口
https://www.python100.com/html/118945.html
nc -u IP地址 端口号
```

### linux查看某个服务具体启动时间:
```
ps -p PID -o lstart,  其中，PID为某个进程的进程ID号
```

### DNS：resolv.conf
```
本机的DNS配置信息是在：/etc/resolv.conf
nameserver 8.8.8.8 #google域名服务器
nameserver 8.8.4.4 #google域名服务器
nameserver 114.114.114.114 #国内第一个、全球第三个开放的全国通用DNS地址,可以为使用者提供高速、稳定、可信的DNS递归解析服务,延迟基本上在10ms以下
nameserver 223.6.6.6 #阿里DNS,延迟在25ms左右
nameserver 223.5.5.5 #阿里DNS
```

/etc/resolv.conf重启被重置：https://blog.csdn.net/nowhy25/article/details/132567625
```
编辑 NetworkManager 的配置文件，禁用其自动更新 /etc/resolv.conf。

使用文本编辑器打开 /etc/NetworkManager/NetworkManager.conf 文件：
sudo vi /etc/NetworkManager/NetworkManager.conf
在 [main] 部分中添加以下行：
dns=none
保存文件并重启 NetworkManager 服务： systemctl restart NetworkManager
```

### 查、杀进程：ps -ef
```
1、ps 命令用于查看当前正在运行的进程。 grep 是搜索例如： ps -ef | grep java  //表示查看所有进程里 CMD 是 java 的进程信息`
2、ps -aux | grep java          //-aux 显示所有状态 `
3. kill 命令用于终止进程例如： kill -9 [PID]   //-9 表示强迫进程立即停止通常用 ps 查看进程 PID ，用 kill 命令终止进程
```

### 查看ip
```
ifconfig
或
ip addr
```

### netstat查看占用端口进程（2种方式）
```
1、lsof -i:端口号

2、查看所有端口
netstat -tunlp

3、查找指定端口
netstat -tunlp|grep 端口号
```

### 查看进程端口（27404为PID）
```netstat -nap | grep 27404```

### 排查网络路由[traceroute](https://blog.csdn.net/yuguo_im/article/details/143920529)
方式1  
```
1、安装yumdownloader命令
yum install yum-utils -y

2、查看系统是否安装了traceroute
rpm -q traceroute

3、下载rpm包
yum install  traceroute  --downloadonly --downloaddir=/usr/local

4、拷贝rpm包到离线环境，执行安装命令
cd  /usr/local
rpm -Uvh --force --nodeps *.rpm

5.排查
traceroute 192.168.1.123

5.指定协议 端口
traceroute -T -p 9100 192.168.1.123
```

方式2  
```yum install traceroute –y```

### tcpdump网络抓包 
- https://luanpeng.blog.csdn.net/article/details/82991778
- https://blog.csdn.net/tian830937/article/details/126101925

``` 
## 安装yum install tcpdump -y

## 抓包
tcpdump -i ifcfg-ens192
tcpdump -i eno16777984 port 3306
tcpdump -n -i any port 8080  ## 不指定协议和网卡
tcpdump -n -i any port 8080 -X -c 100 -w /tmp/tcp.cap ##指定输出外部文件，拷到本地用wireshark打开分析
示例：tcpdump udp -i ens192 and host 192.168.3.200  and port 58913  -s 0  -X -c 100 -w ./dump.cap ##导出文件

示例：tcpdump tcp -i any and host 192.168.3.200 and port 43905 -X -c 100 -w ./spark.cap 
这条tcpdump命令的各个参数的含义如下：
1)-i any: 这是指定要监听的网络接口的参数。在这里，any代表所有可用的网络接口。
2)tcp and host 192.168.3.200: 这是指定要捕获的协议和目标主机IP地址的参数。在这个例子中，我们使用tcp表示只捕获TCP协议的数据包，目标主机的IP地址为192.168.3.200。
3)and port 43905: 这是指定要捕获的目标端口号的参数。在这个例子中，我们使用43905作为目标端口的端口号。
4)-X: 这是启用十六进制格式输出数据包的参数。它可以帮助你更好地理解数据包的内容。
5)-c 100: 这是指定要捕获的数据包数量的参数。在这里，我们使用100表示只捕获100个数据包。当捕获到指定数量的数据包后，tcpdump将停止并退出。
6)-w ./spark.cap: 这是指定输出文件路径和名称的参数。在这个例子中，我们将捕获的数据包保存在当前目录下的spark.cap文件中。
总结起来，这条tcpdump命令将在所有可用的网络接口上监听TCP协议的数据包，目标主机的IP地址为192.168.3.200，目标端口的端口号为43905，捕获100个数据包，并将捕获的数据包保存在当前目录下的spark.cap文件中，每个数据包以十六进制格式显示

示例：tcpdump tcp -i eth1 -t -s 0 -c 100 and dst port ! 22 and src net 192.168.1.0/24 -w ./target.cap
(1)tcp: ip icmp arp rarp 和 tcp、udp、icmp这些选项等都要放到第一个参数的位置，用来过滤数据报的类型
(2)-i eth1 : 只抓经过接口eth1的包
(3)-t : 不显示时间戳
(4)-s 0 : 抓取数据包时默认抓取长度为68字节。加上-S 0 后可以抓到完整的数据包
(5)-c 100 : 只抓取100个数据包
(6)dst port ! 22 : 不抓取目标端口是22的数据包
(7)src net 192.168.1.0/24 : 数据包的源网络地址为192.168.1.0/24
(8)-w ./target.cap : 保存成cap文件，方便用ethereal(即wireshark)分析

## 读取tcpdump保存文件(-r选项)
对于保存的抓包文件，我们可以使用-r选项进行读取：
tcpdump -r 20120606.pcap
```

### wireshark分析抓包内容
https://huaweicloud.csdn.net/6356064ad3efff3090b58d49.html

下载：https://www.wireshark.org/download.html

![分析](https://wdsheng0i.github.io/assets/images/2021/net/wireshark.png)  

### systemd
``` 
systemd服务的启动命令放置在/lib/systemd/system下  

systemctl enable XXX.service命令会在/etc/systemd/system/multi-user.target.wants下创建指向/lib/systemd/system服务的软连接  
系统启动时会读取/etc/systemd/system下的服务  

#查看开机自启服务
systemctl list-unit-files --type=service | grep enabled
```

## 五、文件操作
### grep/sed/awk
linux文本三剑客详解 https://blog.csdn.net/m0_55641973/article/details/131259480

### 新建目录：mkdir
```mkdir opt/test```

### 移动文件： mv
```mv apache-tomcat-7.0.81.tar.gz       /usr```

### 重命名 mv
```mv a.txt  b.txt```

### 复制文件：cp
```cp /home/ftp/FTP-linux.zip      /home/```
```cp -r /home/ftp/      /home/```

### 删除文件: rm
```
rm -rf 文件名  -r  //就是向下递归，不管有多少级目录，一并删除  -f 就是直接强行删除，不作任何提示的意思
rm -f app.log.2024-01-{20..28}.*  //删除多个文件，日期连续的日志文件
```

### 查看编辑文件：cat vi more
```
1.查看 cat  a.txt
2.编辑 vi a.txt 
    i 进入编辑模式， esc 进入命令模式 ，q退出，wq保存退出，q!不保存强制推出
    命令模式下：x删除一个字符，dd删除一行 上下左右键命令模式下有效
    全局搜索：find / -name *minio*
    vim编辑器中搜索： /搜索的内容
    查找下一个：n
3.more 全部读取，按页来查看文件的内容
    Enter 向下n行，可定义定义。默认为1行
    空格键 向下滚动一屏
    Ctrl+B 返回上一屏
    = 输出当前行的行号
    q 退出more
4.less按查看逐页读取，查看大文件
```

### 创建文件touch

### 编辑文件追加内容cat >> file << EOF
``` 
#示例1
cat >> /etc/rancher/k3s/registries.yaml << EOF
mirrors:
  hub.wds.com:
    endpoint:
      - "https://hub.wds.com"
configs:
  "hub.wds.com":
    auth:
      username: wds
      password: wds123
    tls:
      insecure_skip_verify: true
EOF

#示例2
cat >> /etc/hosts << EOF
1.1.4.1 hub.wds.com
EOF

```

### 解压缩：tar unzip
``` 
tar -cvf folder.tar folder/  //压缩文件夹
tar -cvf folder.tar a.txt *.js *.json   //压缩多文件
tar -zcvf folder.tar.gz folder/  //压缩gz 
tar zxvf ./apache-tomcat-7.0.81.tar.gz   //解压tar.gz
tar zxvf ./apache-tomcat-7.0.81.tar.gz  -C /opt/tomcat/  指定解压目录
tar xvf scws-xxx-xx.tar.bz2   //解压tar.bz2
unzip zhparser-master.zip   //解压zip
zip log.zip dump3.cap app.log  //压缩zip 
```

### 文件权限：chmod、chown
``` 
chmod 777 文件名 //文件改权限
chmod 777 -R * //文件夹赋权限
chmod 755 -R * //文件夹赋权限

文件夹赋权账号：chown vftp:vftp download/
```

### 远程拷贝两个主机文件: scp
``` 
1、拷贝本机/home/administrator/test整个目录至远程主机192.168.1.100的/root目录下  
scp -r /home/administrator/test/   root@192.168.1.100:/root/

2、拷贝单个文件至远程主机的/root目录下  
scp  /home/administrator/Desktop/old/driver/test/test.txt   root@192.168.1.100:/root/
其实上传文件和文件夹区别就在参数 -r， 跟cp, rm的参数使用差不多， 文加夹多个 -r
  
3、远程文件/文件夹下载: 把192.168.62.10上面的/root/文件夹，下载到本地的/home/administrator/Desktop/new/下，使用远程端的root登陆  
scp -r root@192.168.62.10:/root/  /home/administrator/Desktop/new/
```

### 远程链接：ssh -p   
```ssh -p 22 root@192.168.0.1```

### sftp操作
```
登陆：sftp -P 12322 ftpuser@192.168.1.1 
cd 到目标目录下
下载：get a.txt /home/
上传：put /home/a.txt
创建目录： mkdir test，
删除目录： rmdir test
```

问题：/etc/ssh/ssh_config line 59: Unsupported option “gssapiauthentication“  
https://huaweicloud.csdn.net/635637fcd3efff3090b5aee9.html? 

问题记录：
登录报错Unsupported option "gssapiauthentication"  
1、打开~/.ssh/known_hosts文件，然后找到对应ip的记录，删除；  
2、ssh-keygen -R +ip ；  

### ftp操作
```
登陆：ftp 192.168.1.1 21
cd 到目标目录下
下载：get a.txt /home/
上传：put /home/a.txt 
```

## 六、系统操作
### CentOS修改服务器系统时间
``` 
linux安装完毕后，一般都是国外的世界，一点都不方便设置任务，或者导致网站获取本地的时间错乱，所以就需要把服务器的时间改为和本地时间一致，也就是换成中国的时间。
第一条指令：date –s '2016-10-31 10:10:10'
第二条指令：clock –w //将日期写入CMOS
时间按照上面的格式，操作完毕可以再用
```

### 查看系统时间
``` 
查看：date
修改：date -s '2019-09-09 10:00:00'
```

### 查看系统版本
``` 
lsb_release -a
或者
cat /etc/centos-release
```

### 创建用户组、用户、密码、用户主目录
```
用户配置文件，存储用户的基本信息 /etc/passwd
存储用户的密码信息 /etc/shadow
存储用户组信息的文件：/etc/group
groupadd hr  ## 新增
groupmod -g 1100 -n bjhr hr  ##修改 -g 新的组ID -n 新的组名 原有组名 
groupdel 用户组名 ## 删除

useradd  -d/home/mysqluser -m mysqluser
```

### 查看主机名、添加、修改hostname、hosts
hostname: 需要保持etc/hostname、etc/hosts本机映射的hostname一致，否则可能会重启后不生效
```
vi etc/hosts   添加一行
192.23.20.72    wds.com    //ip   hostname

vi etc/hostname 
wds.com
```

sed批量修改/etc/hostname： 1c替换文件第一行
```
sed -i "1c devops-`ifconfig ens192 |grep netmask |awk '{print $2}'|sed 's/\./0/g'`"  /etc/hostname
或者
hostnamectl set-hostname "devops-`ifconfig ens192 |grep netmask |awk '{print $2}'|sed 's/\./0/g'|sed 's/01/1/g'`" 
```

sed批量修改/etc/hosts：3a第三行后追加一行
```
sed -i "3a`ifconfig ens192 |grep netmask |awk '{print $2}'`     `hostname`"  /etc/hosts
```

批量改/etc/resolv.conf,第2行后追加一行nameserver 8.8.8.8
``` 
sed -i "2a nameserver 8.8.8.8"  /etc/resolv.conf
```

### 重启：reboot

### 切换root权限sudo
```
su root //需要输入root密码

sudo su //获取root权限，需要输入当前用户app密码，前提是当前用户app需要线加入到/etc/sudoers文件中
如：app ALL=(ALL) NOPASSWD: ALL

sudo su - //获取root权限，带 - 会带上环境变量，有些命令没有环境变量执行不了，如kubectl

sudo -i
```

### 查看历史操作命令： history
```
#历史操作搜索
history|grep 'docker'
```

### 修改用户密码passwd
```
passwd user1

我们有时候要更改linux账户密码时，有时候会遇到下面这种情况：
Password has been already used. Choose another.
passwd: Have exhausted maximum number of retries for service

这个意思是你当前设置的密码最近已经使用过，请重新设置。
找到linux下的一个文件/etc/security/opasswd， 这个文件记录了历史密码，把它清空，或者相应账号注释，再重新设置密码
```

## 七、查看日志
### 查看linux下的日志：tail -f、head -n、cat
``` 
tail -f /var/log/messages
tail -200f /var/log/messages //查看后200行
cat /var/log/*.log

head -n 50 app.log   //查看前50行
```

### 日志在更新，实时查看tail -f
```
tail -f /var/log/messages

还可以使用
watch -d -n 1 cat /var/log/messages   -d表示高亮不同的地方，-n表示多少秒刷新一次。
该指令，不会直接返回命令行，而是实时打印日志文件中新增加的内容，这一特性，对于查看日志是非常有效的。如果想终止输出，按 Ctrl+C 即可。
```

### 日志查看、搜索
```
//查看文件尾200行，并继续实时打印
tail -200f mgmt-info.log

//查看尾部 200 行
tail -n 200 mgmt-info.log
tail -200 mgmt-info.log

//在当前目录下文件mgmt-info.log，搜索 "更新sn为【290200000937】的设备状态为"
grep -i 更新sn为【290200000937】的设备状态为 ./mgmt-info.log
```

### 清空日志cat /dev/null >
cat /dev/null > err.log

### rsyslog系统日志进程占用内存较高
https://www.cnblogs.com/gered/p/17519186.html
```
1.top查看所有进程，m内存大小排序展示

2.查看rsyslog进程
systemctl status  rsyslog.service

3.编辑rsyslog.service限制RSS内存使用，还可以限制CPU、stack段大小、data段大小等
systemctl edit rsyslog.service --full

...
[Service]
LimitRSS=1073741824
...

4.重启服务
systemctl daemon-reload
systemctl restart rsyslog.service

5.检查内存占用
free -m

[root@m1 ~]<20231109 09:26:55># ps -ef|grep rsyslog
root       1123      1  0 09:20 ?        00:00:00 /usr/sbin/rsyslogd -n
root       2601 128627  0 09:26 pts/0    00:00:00 grep --color=auto rsyslog 
[root@m1 ~]<20231109 09:27:05># cat /proc/1123/limits   
Limit                     Soft Limit           Hard Limit           Units     
Max cpu time              unlimited            unlimited            seconds   
Max file size             unlimited            unlimited            bytes     
Max data size             unlimited            unlimited            bytes     
Max stack size            8388608              unlimited            bytes     
Max core file size        0                    unlimited            bytes     
Max resident set          1073741824           1073741824           bytes     
Max processes             31127                31127                processes 
Max open files            1024                 4096                 files     
Max locked memory         65536                65536                bytes     
Max address space         unlimited            unlimited            bytes     
Max file locks            unlimited            unlimited            locks     
Max pending signals       31127                31127                signals   
Max msgqueue size         819200               819200               bytes     
Max nice priority         0                    0                    
Max realtime priority     0                    0                    
Max realtime timeout      unlimited            unlimited            us     
```

### 在Linux系统中，有三个主要的日志子系统
``` 
1.连接时间日志--由多个程序执行，把纪录写入到/var/log/wtmp和/var/run/utmp，login等程序更新wtmp和utmp文件，使系统管理员能够跟踪谁在何时登录到系统。
2.进程统计--由系统内核执行。当一个进程终止时，为每个进程往进程统计文件（pacct或acct）中写一个纪录。进程统计的目的是为系统中的基本服务提供命令使用统
3.错误日志--由syslogd（8）执行。各种系统守护进程、用户程序和内核通过syslog（3）向文件/var/log/messages报告值得注意的事件。另外有许多UNIX程序创建日志。像HTTP和FTP这样提供网络服务的服务器也保持详细的日志。
```

## 八、linux设置服务开机自启
``` 
在/etc/rc.local中添加
如果不想将脚本粘来粘去，或创建链接什么的， 则:
step1. 先修改好脚本，使其所有模块都能在任意目录启动时正常执行;
step2. 再在/etc/rc.local的末尾添加一行以绝对路径启动脚本的行;
如:
$ vim /etc/rc.local
` #!/bin/sh`
` #`
` # This script will be executed *after* all the other init scripts.`
` # You can put your own initialization stuff in here if you don't`
` # want to do the full Sys V style init stuff.`

touch /var/lock/subsys/local
. /etc/rc.d/rc.tune
/opt/pjt_test/test.pl

保存并退出;
再重启动测试下，则在其它的程序都启动完成后，将启动脚本;

这里介绍一下通过chkconfig命令添加脚本为开机自动启动的方法。

1. 编写脚本autostart.sh（这里以开机启动redis服务为例），脚本内容如下：

#!/bin/sh
#chkconfig: 2345 80 90
#description:开机自动启动的脚本程序

# 开启redis服务 端口为6379
/usr/local/service/redis-2.8.3/src/redis-server --port 6379 &

脚本第一行 “#!/bin/sh” 告诉系统使用的shell；
脚本第二行 “#chkconfig: 2345 80 90” 表示在2/3/4/5运行级别启动，启动序号(S80)，关闭序号(K90)；
脚本第三行 表示的是服务的描述信息
注意： 第二行和第三行必写，否则会出现如“服务 autostart.sh 不支持 chkconfig”这样的错误。

2. 将写好的autostart.sh脚本移动到/etc/rc.d/init.d/目录下

3. 给脚本赋可执行权限
cd /etc/rc.d/init.d/
chmod +x autostart.sh

4. 添加脚本到开机自动启动项目中
chkconfig --add autostart.sh
chkconfig autostart.sh on
到这里就设置完成了，我们只需要重启一下我们的服务器，就能看到我们配置的redis服务已经可以开机自动启动了。
```

## 九、硬件信息查看

### lscpu查看cpu
```
lscpu命令，查看的是cpu的统计信息.
blue@blue-pc:~$ lscpu
Architecture:          i686          #cpu架构
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian   #小尾序
CPU(s):                4            #总共有4核
On-line CPU(s) list:   0-3
Thread(s) per core:    1              #每个cpu核，只能支持一个线程，即不支持超线程
Core(s) per socket:    4               #每个cpu，有4个核
Socket(s):             1              #总共有1一个cpu
Vendor ID:             GenuineIntel    #cpu产商 intel
CPU family:            6
Model:                 42
Stepping:              7
CPU MHz:               1600.000
BogoMIPS:              5986.12
Virtualization:        VT-x             #支持cpu虚拟化技术
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              6144K

```

### 查看/proc/cpuinfo,可以知道每个cpu信息，如每个CPU的型号，主频等。
```
# cat /proc/cpuinfo
processor    : 0
vendor_id    : GenuineIntel
cpu family    : 6
model        : 42
model name    : Intel(R) Core(TM) i5-2320 CPU @ 3.00GHz
.....
上面输出的是个cpu部分信息，还有3个cpu信息省略了。
```

### 内存不够，加swap交换扩展内存
```
#count=2048 bs=1MiB表示2GB
dd if=/dev/zero of=/swapfile count=2048 bs=1MiB
mkswap /swapfile
chmod 600 /swapfile
swapon /swapfile
echo "/swapfile swap swap defaults 0 0" >> /etc/fstab
```

### 查看内存硬件信息
```
dmidecode -t memory
# dmidecode 2.11
SMBIOS 2.7 present.
Handle 0x0008, DMI type 16, 23 bytes
Physical Memory Array
Location: System Board Or Motherboard
....
Maximum Capacity: 32 GB
....
Handle 0x000A, DMI type 17, 34 bytes
....
Memory Device
Array Handle: 0x0008
Error Information Handle: Not Provided
Total Width: 64 bits
Data Width: 64 bits
Size: 4096 MB
.....
我的主板有4个槽位，只用了一个槽位，上面插了一条4096MB的内存。
```

### 查看硬盘和分区分lsblk
``` 
# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 465.8G  0 disk
├—sda1   8:1    0     1G  0 part /boot
├—sda2   8:2    0   9.3G  0 part [SWAP]
├—sda3   8:3    0  74.5G  0 part /
├—sda4   8:4    0     1K  0 part
├—sda5   8:5    0 111.8G  0 part /home
└—sda6   8:6    0 269.2G  0 part
```

### 硬盘和分区的详细信息fdisk -l
``` 
# fdisk -l
Disk /dev/sda: 500.1 GB, 500107862016 bytes
255 heads, 63 sectors/track, 60801 cylinders, total 976773168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0x00023728
Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2148351     1073152   83  Linux
/dev/sda2         2148352    21680127     9765888   82  Linux swap / Solaris
/dev/sda3        21680128   177930239    78125056   83  Linux
/dev/sda4       177932286   976771071   399419393    5  Extended/dev/sda5       177932288   412305407   117186560   83  Linux
/dev/sda6       412307456   976771071   282231808   83  Linux
```

### 查看网卡硬件信息lspci | grep -i 'eth'
``` 
# lspci | grep -i 'eth'
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168B PCI Express Gigabit Ethernet controller (rev 06)
```

### 查看系统的所有网络接口ifconfig -a
``` 
# ifconfig -a
eth0      Link encap:以太网  硬件地址 b8:97:5a:17:b3:8f
.....
lo        Link encap:本地环回
.....
或者是
ip link show
1: lo: <LOOPBACK> mtu 16436 qdisc noqueue state DOWN
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
link/ether b8:97:5a:17:b3:8f brd ff:ff:ff:ff:ff:ff

如果要查看某个网络接口的详细信息，例如eth0的详细参数和指标
# ethtool eth0
Settings for eth0:
Supported ports: [ TP MII ]
Supported link modes:   10baseT/Half 10baseT/Full
100baseT/Half 100baseT/Full
1000baseT/Half 1000baseT/Full #支持千兆半双工，全双工模式
Supported pause frame use: No
Supports auto-negotiation: Yes #支持自适应模式，一般都支持
Advertised link modes:  10baseT/Half 10baseT/Full
100baseT/Half 100baseT/Full
1000baseT/Half 1000baseT/Full
Advertised pause frame use: Symmetric Receive-only
Advertised auto-negotiation: Yes #默认使用自适应模式
Link partner advertised link modes:  10baseT/Half 10baseT/Full
100baseT/Half 100baseT/Full
.....
Speed: 100Mb/s #现在网卡的速度是100Mb,网卡使用自适应模式，所以推测路由是100Mb，导致网卡从支  持千兆，变成要支持百兆
Duplex: Full   #全双工
.....
Link detected: yes    #表示有网线连接，和路由是通的
```

### 查看pci信息，即主板所有硬件槽信息。
``` 
Lspci

00:00.0 Host bridge: Intel Corporation 2nd Generation Core Processor Family DRAM Controller (rev 09) #主板芯片
00:02.0 VGA compatible controller: Intel Corporation 2nd Generation Core Processor Family Integrated Graphics Controller (rev 09) #显卡
00:14.0 USB controller: Intel Corporation Panther Point USB xHCI Host Controller (rev 04) #usb控制器
00:16.0 Communication controller: Intel Corporation Panther Point MEI Controller #1 (rev 04)
00:1a.0 USB controller: Intel Corporation Panther Point USB Enhanced Host Controller #2 (rev 04)
00:1b.0 Audio device: Intel Corporation Panther Point High Definition Audio Controller (rev 04) #声卡
00:1c.0 PCI bridge: Intel Corporation Panther Point PCI Express Root Port 1 (rev c4) #pci 插槽
00:1c.2 PCI bridge: Intel Corporation Panther Point PCI Express Root Port 3 (rev c4)
00:1c.3 PCI bridge: Intel Corporation Panther Point PCI Express Root Port 4 (rev c4)
00:1d.0 USB controller: Intel Corporation Panther Point USB Enhanced Host Controller #1 (rev 04)
00:1f.0 ISA bridge: Intel Corporation Panther Point LPC Controller (rev 04)
00:1f.2 IDE interface: Intel Corporation Panther Point 4 port SATA Controller [IDE mode] (rev 04) #硬盘接口
00:1f.3 SMBus: Intel Corporation Panther Point SMBus Controller (rev 04)
00:1f.5 IDE interface: Intel Corporation Panther Point 2 port SATA Controller [IDE mode] (rev 04) #硬盘接口
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168B PCI Express Gigabit Ethernet controller (rev 06) #网卡
03:00.0 PCI bridge: Integrated Technology Express, Inc. Device 8893 (rev 41)
如果要更详细的信息:lspci -v 或者 lspci -vv
如果要看设备树:lscpi –t
```

### 查看bios信息
``` 
# dmidecode -t bios
......
BIOS Information
Vendor: American Megatrends Inc.
Version: 4.6.5
Release Date: 04/25/2012
.......
BIOS Revision: 4.6
......

dmidecode以一种可读的方式dump出机器的DMI(Desktop Management Interface)信息。这些信息包括了硬件以及BIOS，既可以得到当前的配置，也可以得到系统支持的配置，比如说支持的内存数等。
```

### 如果要查看所有有用信息
dmidecode –q

以上是linux查看硬件信息的所有命令，可以查看CPU、硬盘、网卡、磁盘等硬件的信息

## 附：常用命令
- 1.查看所有端口：netstat -tunlp
- 2.查看端口：netstat -tunlp | grep 8080
- 3.搜索程序安装位置：whereis nginx
- 4.查找文件：find / -name nginx
- 5.历史命令：history
- 6.查看出口ip：curl cip.cc 或 curl -4s https://ipinfo.io
- 7.查看内存使用：free -m
- 8.查看磁盘使用：df -h
- 8.查看文件大小：ll -lh
- 9.磁盘分区：lsblk
- 10.防火墙状态：systemctl status firewalld
- 11.查看文件大小：
  - du -h --max-depth=1 
  - du -hd1
- 13.文件夹赋权账号：
  - chown vftp:vftp download/
  - chmod 755 file、
  - chmod -R 755 dir
- 14.远程copy：scp a.txt root@192.168.0.1:/home
- 15.远程链接：ssh -p 22 root@192.168.0.1
- 16.软连接：ln -snf /data/packages/demo-h5/v1.2.1 demo-h5  # 当前目录下demo-h5,软连接到/data/packages/demo-h5/v1.2.1
- 17.查看白名单：iptables -nL --line-number
- 18.修改密码：passwd appUser
- 19.chage -M 99999 ansible
- 20.统计目录下文件数：ls -Rl | grep '.txt' | wc -l
- 21.查看定时任务：crontab -l
- 22.不删文件，清空内容：cat /dev/null > /data/logs/app.log  
- 23.[curl命令查看请求响应时间](https://blog.csdn.net/fang0604631023/article/details/127845928)：
  -[ curl的常用参数](https://blog.csdn.net/fen_dou_shao_nian/article/details/123038537) 
  - curl -o /dev/null -s -w %{time_namelookup}::%{time_connect}::%{time_starttransfer}::%{time_total}::%{speed_download}"\n" "https://www.baidu.com"
  - curl -o /dev/null -s -w %{time_namelookup}::%{time_connect}::%{time_starttransfer}::%{time_total}::%{speed_download}"\n" "https://baidu.com"
  - curl -o /dev/null -s -w %{time_namelookup}::%{time_connect}::%{time_starttransfer}::%{time_total}::%{speed_download}"\n" "https://blog.csdn.net"
  - curl -o /dev/null -s -w "time_connect: %{time_connect}\ntime_starttransfer: %{time_starttransfer}\ntime_total: %{time_total}\n" "https://blog.csdn.net/"
  - curl -i -v  -X get "http://10.168.1.168:30201/onesupport/entrancexl" -H "content-type:application/json;charset=UTF-8"
  - curl -i -v  -X post "http://10.168.1.168:30201/onesupport/entrancexl" -H "content-type:application/json;charset=UTF-8" -d '{"name":"zhangsan"}'
  - curl -k -I -i -u testUser:pwd https://ip:port
- 24.iptable添加白名单:https://baijiahao.baidu.com/s?id=1765288969345841746&wfr=spider&for=pc

``` 
https://blog.csdn.net/wejack/article/details/121677438
https://blog.csdn.net/weixin_40575457/article/details/123315023

#查看规则及序号
iptables -nvL --line-number

# 添加ACCEPT规则
iptables -A INPUT -s 192.168.123.1 -p all -j ACCEPT   //-A，追加规则，在最后,all所有协议、所有端口
iptables -I INPUT -s 192.168.123.1 -p tcp --dport 3306 -j ACCEPT  //-I,插入到第一条，指定ip、指定协议、指定端口
iptables -I INPUT -s 192.168.123.1/24 -p tcp --dport 3306 -j ACCEPT  //-I,插入到第一条，加子网段；accept规则要放在REJECT all 前面
iptables -I INPUT -s 221.131.136.0/24 -p tcp -m multiport --dports 8080,3306 -j ACCEPT
iptables -I INPUT -s 221.131.136.154 -p tcp -m state --state NEW --dport 8083-j ACCEPT
iptables -I INPUT -s 221.131.136.154 -p tcp -m state --state NEW -m multiport --dports 8083,22 -j ACCEPT
iptables -I INPUT -m iprange --src-range 192.168.123.100-192.168.123.200 -j ACCEPT   //加ip段
iptables -I IN_public_allow -s 192.168.123.1/24 -p tcp -m tcp --dport 3306 -m conntrack --ctstate NEW,UNTRACKED -j ACCEPT  

#添加DROP规则
iptables -I INPUT -s 192.168.123.1 -j DROP
iptables -I INPUT -s 121.0.0.0/24 -j DROP
iptables -I INPUT 4 -s 45.152.65.0/24 -j DROP  ## 在序号第3条后插一条drop规则
dockeiptables -A INPUT -p tcp -m multiport --dports 22,5901,8080 -s 59.45.175.0/24 -j DROP   #如何对多个端口进行匹配

#添加REJECT规则
iptables -A INPUT -p all -j REJECT
iptables -A INPUT -p tcp --dport 22 -j REJECT
iptables -A INPUT -p tcp -m multiport --dports 8080,3306,1234 -j REJECT
iptables -A INPUT -p tcp --dport 22:28 -j REJECT   #如何对多个端口进行匹配

#删除规则：先查看规则及序号iptables -nL --line-number，然后删除对应序号那条即可
iptables -D INPUT 3

#保存规则
service iptables save

#或者直接编辑
vi /etc/sysconfig/iptables
service iptables reload

#开启iptables日志记录,即iptables添加一条规则
iptables -A INPUT -j LOG --log-prefix "iptables_reject: "
iptables -A INPUT -p tcp --dport 2181 -j LOG --log-prefix "INPUT: "

#查看日志
journalctl | grep -i "iptables_reject"

#删除日志记录对于规则id，停止记录日志
iptables -D INPUT  id
```

- 25.firewall添加白名单

``` 
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0.1/24" port protocol="tcp" port="3306" accept"
firewall-cmd --reload
firewall-cmd --list-all 
firewall-cmd --permanent --add-rich-rule='rule protocol value="vrrp" accept'  ## 防火墙开启vrrp 虚拟路由冗余协议(Virtual Router Redundancy Protocol，简称VRRP)
```

- 26.docker进程加iptables白名单: https://www.cnblogs.com/xiongzaiqiren/p/iptables.html

``` 
# 亲测不可用（可能与docker版本有关）
iptables -I DOCKER-USER -i ext_if ! -s 172.0.0.0/24 -p tcp --dport 9180 -j DROP
iptables -I DOCKER -i ext_if ! -s 172.0.0.0/24 -p tcp --dport 9180 -j DROP
iptables -I DOCKER -s 172.0.0.0/24 -p tcp --dport 9180 -j ACCEPT
iptables -I DOCKER -p tcp --dport 9180 -j DROP
iptables -A DOCKER -p tcp --dport 9180 -j REJECT

## 亲测可用: 在DOCKER-USER链中，限制虚拟机网卡，访问容器内8080端口                                                          
iptables -I DOCKER-USER -i ens192 -p tcp --dport 8080 -j DROP # 1.先drop所有
iptables -I DOCKER-USER -i ens192 -s 172.0.0.0/24 -p tcp --dport 8080 -j ACCEPT   # 2.再接收172.0.0.0/24：ens192->虚拟机网卡,8080->容器内端口

## 或者在虚拟机上接收所有，不做限制，然后请求可以正常转发到docker容器内
iptables -I INPUT  -p tcp -m state --state NEW -m multiport --dports 8083 -j ACCEPT
```

## 附图：
![](https://wdsheng0i.github.io/assets/images/2021/os/Linux-1.png)

## 问题记录
1.Errors during downloading metadata for repository 'update-source':
```
检查网络连接：确保你的系统可以正常访问互联网。
清除缓存：运行 yum clean all 或 dnf clean all 清除缓存，然后再尝试更新
```