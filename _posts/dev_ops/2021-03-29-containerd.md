---
layout: post
title: containerd容器
category: dev-ops
tags: [dev-ops]
---

## 参考资料
- 官网地址：https://containerd.io/
- 下载地址：https://containerd.io/downloads/
- 文档地址：https://containerd.io/docs/

## 1.containerd简介
containerd是一个工业级标准的容器运行时，它强调简单性、健壮性和可移植性。containerd可以在宿主机中管理完整的容器生命周期，包括容器镜像的传输和存储、容器的执行和管理、存储和网络等。

containerd是从Docker中分离出来的一个项目，可以作为一个底层容器运行时，现在它成了Kubernete容器运行时更好的选择。k3s新版本的容器运行时已经换成了containerd

## 2.containerd安装部署
https://cloud.tencent.com/developer/article/2274551  
``` 
sudo yum install containerd
```

## 3.containerd基础命令
https://blog.51cto.com/u_12207/7547884

基本规则：将docker命令中的docker 替换成crictl     
```crictl ps 、crictl pull.....  ```

``` 
#查看 containerd 命名空间
[root@xianchaomaster1 ~]# ctr namespace ls
#查看默认名称空间镜像有哪些
[root@xianchaomaster1 ~]# ctr image ls
#查看 k8s 命名空间下的镜像
[root@xianchaomaster1 ~]# ctr -n=k8s.io images ls
#拉取 busybox 镜像
[root@xianchaomaster1 ~]# ctr image pull docker.io/library/busybox:latest
注：必须全路径，从 dockerhub 上下载默认 busybox 镜像。
[root@xianchaomaster1 ~]# ctr images rm 删除镜像
#压缩镜像
[root@xianchaomaster1 ~]# ctr images pull docker.io/library/mysql:latest
[root@xianchaomaster1 ~]# ctr images export mysql.tar.gz 
docker.io/library/mysql:latest
#导出镜像
[root@xianchaomaster1 ~]# ctr images import mysql.tar.gz
```

## 问题记录