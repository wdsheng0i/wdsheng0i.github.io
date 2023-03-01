---
layout: post
title: Windows使用总结
category: operate-system
tags: [operate-system]
--- 

windows使用总结  

## 系统安装
- [Win7原版(自动激活)Win7 64位旗舰版[安装版]iso镜像(带USB3.0支持8,9代CPU)](http://www.winwin7.com/Win7QiJianBan/Win7AZB-3998.html)
- [U盘安装盘制作方法](http://www.winwin7.com/JC/AnZhuang-37.html)
- [如何设置u盘启动?bios设置u盘启动教程 U盘启动快捷键大全](http://www.winwin7.com/JC/AnZhuang-4.html)
- [Win7安装版u盘安装教程](http://www.winwin7.com/JC/AnZhuang-662.html)
- winxp安装密钥：MRX3F-47B9T-2487J-KWKMF-RPWBY

## 一、cmd常用命令
1.cmd常用命令  
```
查看当前目录下文件：dir
进入分区: d + : 回车  
进入目录：cd  ../bin  
物理地址查看：ipconfig/all
```
    
2.输出文件目录结构 ：tree、tree /f 命令   
```
使用tree /f > tree.txt命令，就可以把生成的文件目录树形结构写入到tree.txt文件中了
还可以使用tree /a等命令，来生成想要的文件目录
```

3.查看出口ip（外网ip）
``` 
windows：cmd 
telnet cip.cc

centos:
curl cip.cc
```

问题：curl: (6) Could not resolve host: cip.cc; 未知的错误
``` 
// 原因：外网使用正常，证明提供服务未出问题。内部访问报错’dns找不到这个网址‘，可能是某dns服务器或dns解析出现了问题。
//修改或添加dns(dns服务器地址可网上查询)
vi /etc/resolv.conf
nameserver 114.114.114.114
nameserver 223.5.5.5
```

## 二、磁盘管理Diskpart
Diskpart命令是Windows环境下的一个命令，利用diskpart可实现对硬盘的分区管理，包括创建分区、删除分区、合并(扩展)分区，完全可取代分区魔术师等第三方工具软件，它还有分区魔术师无法实现的功能，如设置动态磁盘、镜像卷等，而且设置分区后不用重启电脑也能生效。

正常运行该命令时需要系统服务的支持，所以在纯DOS、XP内核的WinPE环境下都是不能运行的，但是在Windows 7的预安装环境下却是可以的。利用Diskpart命令来分区，既不用借助第三方工具，也不会产生100MB的“系统保留”分区，其次分区操作直接设置即刻生效，不用重新启动计算机。

### 2.1 利用Diskpart命令分区方法：
1.当安装程序运行到创建磁盘分区界面时→按下“Shift+F10”→启动命令窗口。
    
2.键入“Diskpart”→回车→进入“Diskpart”的命令环境，其提示符为“DISKPART>”。
    
3.在此提示下键入相应命令就可以进行分区操作，具体用到的命令有：
```
Clean
Lis
Select
Create
Format
Exit
```

这些命令的使用方法可以在Diskpart命令提示符下键入Help或者通过网络查询，如不清楚可以查看。

4.使用“List Disk”命令显示的目标磁盘若为“1”号，则建立分区的步骤如下：
- List Disk：显示本机的所有磁盘，以便正确操作目标磁盘
- Select Disk 1：选择0号磁盘
- Clean ：清除0号磁盘上的所有分区
- Create ：Partition Primary Size=512000 创建主分区，容量为：512000MB
- Active：激活主分区
- Format Quick：快速格式化当前分区
- Create Partition Extended：创建扩展分区
- Create Partition Logical Size=512000：创建逻辑分区一，容量为：512000MB
- Format Quick：快速格式化当前分区
- Create Partition Logical Size=512000：创建逻辑分区二，容量为：512000MB
- Format Quick ：快速格式化当前分区
- Create Partition Logical ：创建逻辑分区三，大小为剩余的容量
- Format Quick ：快速格式化当前分区
- Exit ：退出Diskpart命令环境
- Exit ：退出命令窗口

这里要注意的是Diskpart分区时，一定要选对目标磁盘，当前被选中的磁盘/分区前面会有*号标志，可以用List Disk/Partition来进行查看。另外在分区前也可以用Resan重新扫描一下机器的磁盘，以便正确选择目标磁盘。

5.这时用户再点击“您想将Windows安装在何处?”窗口中的“刷新”选项后就会出现刚才分区的结果，至此，分区操作结束。

通过以上介绍，相信大家对利用Diskpart命令为Windows 7安装分区的方法有了一定的了解，安装分区时不使用第三方软件，即可对硬盘进行分区，轻松解决Windows 7系统分区的问题。

### 2.2 diskpart扩展windows磁盘空间
Diskpart.exe 实用程序在命令行界面中管理磁盘、分区和卷。    
Diskpart.exe 在基本磁盘和动态磁盘上都可以使用。如果 NTFS 卷所在的硬件 RAID 5 容器具有向容器中添加空间的能力，您可以使用 Diskpart.exe 扩展 NTFS 卷，同时将磁盘保持为基本磁盘。

> 使用 extend 命令可以在保留数据的同时将未分配的空间合并到现有卷中。

下面是对 extend 命令的要求： • 卷必须用 NTFS 文件系统格式化。
    
- 对于基本卷，扩展的未分配空间必须是同一磁盘上的下一个相邻空间。
- 对于动态卷，未分配空间可以是系统上任何动态磁盘上的空白空间。
- 只支持数据卷扩展。系统卷或启动卷无法扩展，若扩展它们可能会收到以下错误消息：

DiskPart 未能扩展卷。请确定卷是有效的，可以扩展       
如果系统文件在此分区，您就不能扩展该分区。请将系统页面文件移动到一个不希望扩展的分区。  

> 要扩展分区或卷，必须先选择该卷以将焦点移到该卷，然后可以指定扩展的大小。步骤如下：

1. 在命令提示符下，键入 diskpart.exe。

2. 键入 list volume 以显示计算机上的现有卷。

3. 键入 Select volume volume number，其中 volume number 是要扩展的卷的编号。

4. 键入 extend [size=n] [disk=n] [noerr]。 下面将介绍这些参数：
    size=n
    要向当前分区添加的空间大小（以 MB 为单位）。如果您没有指定大小，磁盘将一直扩展到全部占用下一个相邻的未分配空间。

    disk=n
    在其上扩展卷的动态磁盘。将在该磁盘上分配“size=n”的空间。如果没有指定磁盘，则在当前磁盘上扩展卷。

    noerr
    仅用于脚本撰写。当发生错误时，此参数指定 Diskpart 继续处理命令，就像没有发生错误一样。如果没有指定 noerr 参数，错误将导致 Diskpart 退出并显示错误代码。

5. 键入 exit 退出 Diskpart.exe。

当 extend 命令完成时，您应该收到一则消息，说明 Diskpart 已成功扩展卷。新的空间应该已添加到现有驱动器，同时保留卷上的数据。

注意：在 Microsoft Windows XP 和 Microsoft Windows 2000 中，不能使用 Diskpart.exe 扩展动态磁盘上的最初在基本磁盘上创建的简单卷。只能扩展磁盘升级到动态磁盘后创建的简单卷。如果您试图扩展动态磁盘上的最初在基本磁盘上创建的简单卷，将收到以下错误消息。该限制已在 Microsoft Windows Server 2003 中去除。 

注意：Windows Server 2003 和 Windows XP 中包含 Diskpart.exe，并将其作为基本操作系统的一部分。

要下载用于 Windows 2000 的 Diskpart.exe 命令行实用程序，请访问以下 Microsoft 网站：

http://www.microsoft.com/downloads/details.aspx?FamilyID=0fd9788a-5d64-4f57-949f-ef62de7ab1ae&displaylang=en

注意：我们建议您在转换为动态磁盘之前，联系您的系统供应商以获得更新的 BIOS、固件、驱动程序和代理程序。

## 三、同域网远程连接 
### 3.1 mstsc
1.启用对远程电脑的连接：  
控制面板 ---系统和安全---系统---远程设置，允许**，允许**
    
2.win+r ----->mstsc-->ip(要连接的ip)（如：192.168.1.103 ）--->（目标计算机的用户名和密码 用户名+密码)

3.别人无法访问自己的ip      
解决方案：关闭防火墙    
win7:控制面板 ->Windows 防火墙->  点击左侧的“打开或关闭 Windows 防火墙”

### 3.2 TeamViewer 

### 3.3 向日葵

## 四、双屏设置
主机需要有相关接口，支持双屏接入  

1.有些电脑主板不需要设置bios，直接连接即可

2.有些需要设置BIOS
- F1进入bios
- Device->vidio->设为IGD
- multi  monitor  设为Enble     //允许双屏显示
