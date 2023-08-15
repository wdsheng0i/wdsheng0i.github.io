---
layout: post
title: Seaweedfs 安装、配置、使用 
category: dev-ops
tags: [dev-ops]
---

seaweedfs 安装、配置、使用 

## 参考资料

## 一、部署SeaweedFS
1.下载  

```
https://github.com/chrislusf/seaweedfs/releases
```
2.解压  

```
tar -xzvf linux_amd64.tar.gz
```
3.启动 
 
```
# 
nohup /data/file-server/weed master -port=9333 -defaultReplication="000" >>/data/file-server/log.log &
# 
nohup /data/file-client/weed volume -dir="/data/fsdata/vol1" -mserver="192.168.0.1:9333" -max=500 -ip=192.168.0.1 -port=10001 >>/data/file-client/log.log &
```

## 二、组件集成api使用

 