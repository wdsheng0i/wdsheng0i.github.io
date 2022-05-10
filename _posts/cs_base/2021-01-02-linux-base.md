---
layout: post
title: Linux基础操作命令
category: operate-system
tags: [operate-system]
---  

Linux基础操作命令

## 参考资料

## 一、装机
### 1.1 做raid   
[图文并茂 RAID 技术全解 – RAID0、RAID1、RAID5、RAID100](https://mp.weixin.qq.com/s/XgHz65Fe5WogTxBp0kqs7w)

### 1.2装系统 

### 1.3 配ip：
1.虚拟机装centos7后，ifconfig无ip地址  
1.1 获取动态ip
```
修改/etc/sysconfig/network-scripts/ifcfg-eno16777736  
ONBOOT=no, 将no  改为yes
service network restart

//inux 已修改ip地址（ONBOOT=yes）,但是不显示ip地址??
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
```

2.windows无法ping通服务器  
```
1.检查防火墙，
2.window虚拟网卡是否启用
```

3.查看出口ip（外网ip）
```
windows：cmd 
telnet cip.cc

centos:
curl cip.cc

问题：curl: (6) Could not resolve host: cip.cc; 未知的错误
// 原因：外网使用正常，证明提供服务未出问题。内部访问报错’dns找不到这个网址‘，可能是某dns服务器或dns解析出现了问题。
//修改或添加dns(dns服务器地址可网上查询)
vi /etc/resolv.conf
nameserver 114.114.114.114
nameserver 223.5.5.5
```

###  hostname设置
```
查看 hostname

临时设置 hostname bigdata01

永久设置 vi /etc/hostname，填写 bigdata01 
```

### hosts文件修改 
```
vi /etc/hosts
ip1 hostname1
ip2 hostname2
ip3 hostname3
```

### 防火墙
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

### 挂载存储

### 虚拟机克隆
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
- /home：用户软件安装目录
- /etc：配置文件目录
- /data: 自建用户数据目录

## 三、软件安装
### 安装方式  
`方式1，在线安装：yum install gcc`  
`方式2，离线安装：rpm -ivh vsftpd.rpm   --https://centos.pkgs.org/`

### 查看是否安装  
`rpm -qa |grep yum `  

### 替换yum源 
`/etc/yum.repos.d/CentOS-Base.repo`

### 下载
`wget http://dlcdn.apache.org/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz`

### 安装 git 
`yum install git -y`

### 安装 wget 
`yum -y install wget`

### 安 装vim  
`yum install vim -y`

### 安装 telnet 
`yum install telnet`

### 安装 ifconfig  
`yum install net-tools`  
`yum install ifconfig`
 

### 安装zip解压缩工具 
` yum install unzip`
 

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

## 四、系统、硬件监控命令
### 查看内存
```
free -m 
```

### 查看磁盘
```
df -h 
```

### 查看进程、cpu
```
top 
```

### 查看 内存 cpu 进程 io 使用情况
```
vmstat
```

### 查看系统中文件的使用情况
```
df -h
```

### 查看当前目录下各个文件及目录占用空间大小
```
du -sh * 
```  
```
du -lh --max-depth=1 
```

### 网络是否连通
```
ping ip 
```

### 端口是否可通 
```
telnet ip port 
```

### linux查看某个服务具体启动时间:
```
ps -p PID -o lstart,  其中，PID为某个进程的进程ID号
```

### DNS：
```
linux下好像没有专门的DNS查看命令。
用ifconfig命令也是看不到DNS的信息。 
本机的DNS配置信息是在：/etc/resolv.conf  
```

### 查、杀进程：
``` 
1、ps 命令用于查看当前正在运行的进程。 grep 是搜索例如： ps -ef | grep java  //表示查看所有进程里 CMD 是 java 的进程信息`
2、ps -aux | grep java          //-aux 显示所有状态 `  
3. kill 命令用于终止进程例如： kill -9 [PID]   //-9 表示强迫进程立即停止通常用 ps 查看进程 PID ，用 kill 命令终止进程
```     

### 查看ip
```
ifconfig
```

### 查看占用端口进程（2种方式）
```
1、lsof -i:端口号  
2、netstat -tunlp|grep 端口号
``` 

### 查看进端口（27404为PID）  
netstat -nap | grep 27404  

## 五、文件操作
### 新建目录：
mkdir opt/wenjianjia1   

### 移动文件： 
mv apache-tomcat-7.0.81.tar.gz       /usr

### 复制文件：
cp /home/ftp/FTP-linux.zip      /home/

### 删除文件; 
rm -rf 文件名  -r  
` 就是向下递归，不管有多少级目录，一并删除  -f 就是直接强行删除，不作任何提示的意思`

### 编辑文件：
    1.查看cat  a.txt
    2.编辑vi a.txt
    3.root编辑 sudo vi a.txt
    4.i 进入编辑模式， esc 进入命令模式 ，q退出，wq保存退出
    5.命令模式下：x删除一个字符，dd删除一行 上下左右键命令模式下有效 
    6.全局搜索：find / -name *minio*
    7.vim编辑器中搜索： /搜索的内容
    8.查找下一个：n

### 解压缩：
    tar zxvf ./apache-tomcat-7.0.81.tar.gz   //解压tar.gz
    tar zxvf ./apache-tomcat-7.0.81.tar.gz  -C /opt/tomcat/  指定解压目录
    unzip zhparser-master.zip   //解压zip
    tar xvf scws-xxx-xx.tar.bz2   //解压tar.bz2

### 文件权限：
    chmod 777 文件名 //赋权限    
    chmod 777 -R * //文件夹赋权限
    chmod 755 -R * //文件夹赋权限

### 远程拷贝两个主机文件
    1、拷贝本机/home/administrator/test整个目录至远程主机192.168.1.100的/root目录下 
        scp -r /home/administrator/test/   root@192.168.1.100:/root/
    2、拷贝单个文件至远程主机的/root目录下 
        scp  /home/administrator/Desktop/old/driver/test/test.txt   root@192.168.1.100:/root/
        其实上传文件和文件夹区别就在参数 -r， 跟cp, rm的参数使用差不多， 文加夹多个 -r
    3、远程文件/文件夹下载: 把192.168.62.10上面的/root/文件夹，下载到本地的/home/administrator/Desktop/new/下，使用远程端的root登陆
        scp -r root@192.168.62.10:/root/  /home/administrator/Desktop/new/ 

## 六、系统操作 
### CentOS修改服务器系统时间  
    linux安装完毕后，一般都是国外的世界，一点都不方便设置任务，或者导致网站获取本地的时间错乱，所以就需要把服务器的时间改为和本地时间一致，也就是换成中国的时间。  
    第一条指令：date –s '2016-10-31 10:10:10'  
    第二条指令：clock –w //将日期写入CMOS  
    时间按照上面的格式，操作完毕可以再用

### 查看系统时间：  
    查看：date  
    修改：date -s '2019-09-09 10:00:00'

### 查看系统版本 
    lsb_release -a  
    或者cat /etc/centos-release 

### 创建用户、密码、用户主目录：  
    useradd  -d/home/mysqluser -m mysqluser

### 查看主机名、添加、修改hostname
    etc/hosts   添加一行 
    172.23.20.72    wds    //ip   hostname

### 重启：
reboot  

### 切换root用户：
su root

## 七、查看日志
### 如何实时查看linux下的日志：  
    tail -f /var/log/messages  
    cat /var/log/*.log

### 如果日志在更新，如何实时查看  
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

### 在Linux系统中，有三个主要的日志子系统 
    1.连接时间日志--由多个程序执行，把纪录写入到/var/log/wtmp和/var/run/utmp，login等程序更新wtmp和utmp文件，使系统管理员能够跟踪谁在何时登录到系统。  
    2.进程统计--由系统内核执行。当一个进程终止时，为每个进程往进程统计文件（pacct或acct）中写一个纪录。进程统计的目的是为系统中的基本服务提供命令使用统
    3.错误日志--由syslogd（8）执行。各种系统守护进程、用户程序和内核通过syslog（3）向文件/var/log/messages报告值得注意的事件。另外有许多UNIX程序创建日志。像HTTP和FTP这样提供网络服务的服务器也保持详细的日志。  

## 八、linux设置服务开机自启  
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
        注意： 第二行和第三行必写，负责会出现如“服务 autostart.sh 不支持 chkconfig”这样的错误。
    
    2. 将写好的autostart.sh脚本移动到/etc/rc.d/init.d/目录下
    
    3. 给脚本赋可执行权限
    cd /etc/rc.d/init.d/
    chmod +x autostart.sh
    
    4. 添加脚本到开机自动启动项目中
    chkconfig --add autostart.sh
    chkconfig autostart.sh on
    到这里就设置完成了，我们只需要重启一下我们的服务器，就能看到我们配置的redis服务已经可以开机自动启动了。

## 九、硬件信息查看

### 查看cpu
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
     
    查看/proc/cpuinfo,可以知道每个cpu信息，如每个CPU的型号，主频等。
     
    #cat /proc/cpuinfo
    processor    : 0
    vendor_id    : GenuineIntel
    cpu family    : 6
    model        : 42
    model name    : Intel(R) Core(TM) i5-2320 CPU @ 3.00GHz
    .....
    上面输出的是个cpu部分信息，还有3个cpu信息省略了。

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
 
### 查看硬盘和分区分
    # lsblk
    NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
    sda      8:0    0 465.8G  0 disk
    ├—sda1   8:1    0     1G  0 part /boot
    ├—sda2   8:2    0   9.3G  0 part [SWAP]
    ├—sda3   8:3    0  74.5G  0 part /
    ├—sda4   8:4    0     1K  0 part
    ├—sda5   8:5    0 111.8G  0 part /home
    └—sda6   8:6    0 269.2G  0 part
 
### 如果要看硬盘和分区的详细信息
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
 
### 查看网卡硬件信息
    # lspci | grep -i 'eth'
    02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168B PCI Express Gigabit Ethernet controller (rev 06)
 
### 查看系统的所有网络接口
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
 
### 查看pci信息，即主板所有硬件槽信息。
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
 
### 查看bios信息
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
 
### 如果要查看所有有用信息
    dmidecode –q
     
    以上是linux查看硬件信息的所有命令，可以查看CPU、硬盘、网卡、磁盘等硬件的信息
    
附图：  
![](https://wdsheng0i.github.io/assets/images/2021/os/Linux-1.png)