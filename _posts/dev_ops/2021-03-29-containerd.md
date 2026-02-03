---
layout: post
title: containerd容器
category: dev-ops
tags: [ dev-ops ]
---

## 参考资料

- 官网地址：https://containerd.io/
- 下载地址：https://containerd.io/downloads/
- 文档地址：https://containerd.io/docs/
- Containerd ctr、crictl、nerdctl 客户端命令介绍与实战操作 https://www.modb.pro/db/485911
- ctr命令的基本使用与技巧 https://blog.csdn.net/weixin_44416039/article/details/139365286

## 1.containerd简介

containerd是一个工业级标准的容器运行时，它强调简单性、健壮性和可移植性。containerd可以在宿主机中管理完整的容器生命周期，包括容器镜像的传输和存储、容器的执行和管理、存储和网络等。

containerd是从Docker中分离出来的一个项目，可以作为一个底层容器运行时，现在它成了Kubernete容器运行时更好的选择。k3s新版本的容器运行时已经换成了containerd

## 2.containerd安装部署

https://cloud.tencent.com/developer/article/2274551

``` 
sudo yum install containerd
```

## 3.containerd基础命令crictl、ctr

https://blog.51cto.com/u_12207/7547884

基本规则：将docker命令中的docker 替换成crictl

```
crictl ps
crictl pull
crictl images.....  
```

``` 
#查看 containerd 命名空间
ctr namespace ls

#查看默认名称空间镜像有哪些
 ctr image ls

#查看 k8s 命名空间下的镜像
ctr -n=k8s.io images ls 

#拉取 busybox 镜像
ctr image pull docker.io/library/busybox:latest
注：必须全路径，从 dockerhub 上下载默认 busybox 镜像。

# 跳过证书、验证鉴权参数
ctr image pull --skip-verify -u username:pwd docker.io/library/busybox:latest

#删除镜像
ctr images rm 删除镜像

#拉取镜像
ctr images pull docker.io/library/mysql:latest

#导出镜像
ctr images export mysql.tar.gz  docker.io/library/mysql:latest
ctr -n k8s.io images export kafka-exporter.tar docker.io/danielqsj/kafka-exporter:v1.4.2

#导入镜像 
ctr images import mysql.tar.gz
ctr -n k8s.io i import kafka-exporter.tar
```

## nerdctl管理K3s默认容器运行时containerd://1.5.9-k3s1

https://blog.csdn.net/ly_cyz/article/details/141924837  
在K3s中像使用Docker一样使用Containerd: https://forums.rancher.cn/t/k3s-docker-containerd/700

``` 
安装并配置 Nerdctl
从 nerdctl 的 release（https://github.com/containerd/nerdctl/releases） 26 中下载二进制文件，然后将 nerdctl 移动到/usr/local/bin 下即可完成安装。

因为上一步安装的 K3s 中已经包含了 containerd，所以只需要下载nerdctl-<VERSION>-<OS>-<ARCH>.tar.gz 即可，否则需要安装nerdctl-full-<VERSION>-<OS>-<ARCH>.tar.gz

如果要使用 nerdctl 管理 K3s 环境中的容器，还需要手动指定 containerd socket：
root@k3s:~# export CONTAINERD_ADDRESS="unix:///run/k3s/containerd/containerd.sock"
    
接下来，就可以使用 nerdctl 来查询 K3s 中的容器了：
root@k3s:~# nerdctl -n k8s.io ps 

#导出导入镜像
nerdctl save -o  mirrored-pause.tar rancher/mirrored-pause:3.6 -n  k8s.io
nerdctl -n k8s.io load -i mirrored-pause.tar
```

## 问题记录