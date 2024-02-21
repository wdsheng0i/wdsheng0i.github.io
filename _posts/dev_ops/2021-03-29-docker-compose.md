---
layout: post
title: 容器编排 Docker Compose
category: dev-ops
tags: [dev-ops]
---

容器编排 Docker Compose

## 参考资料

## 1、安装Docker Compose

``` 
# Compose目前已经完全支持Linux、Mac OS和Windows，在我们安装Compose之前，需要先安装Docker。下面我 们以编译好的二进制包方式安装在Linux系统中。 
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
如果不能下载，浏览器访问https://github.com/docker/compose/releases?expanded=true&page=6&q=1.21下载后上传至服务器

# 设置文件可执行权限 
chmod +x /usr/local/bin/docker-compose

# 查看版本信息 
docker-compose -version
```

## 2、卸载Docker Compose

```
# 二进制包方式安装的，删除二进制文件即可
rm /usr/local/bin/docker-compose
```

## 3、使用docker compose编排nginx+springboot项目

```
## 1).创建docker-compose目录
mkdir ~/docker-compose
cd ~/docker-compose
```

```
## 2).编写 docker-compose.yml 文件
version: '3'
services:
  nginx:
   image: nginx
   ports:
    - 80:80
   links:
    - app
   volumes:
    - ./nginx/conf.d:/etc/nginx/conf.d
  app:
    image: app
    expose:
      - "8080"
```

```
## 3).创建./nginx/conf.d目录
mkdir -p ./nginx/conf.d
```

```
## 4).在./nginx/conf.d目录下 编写itheima.conf文件
server {
    listen 80;
    access_log off;

    location / {
        proxy_pass http://app:8080;
    }
   
}
```

```
## 5).在~/docker-compose 目录下 使用docker-compose 启动容器  
docker-compose pull
docker-compose -f ./docker-compose.yml down
docker-compose -f ./docker-compose.yml up -d --remove-orphans  ## --remove-orphans: 删除没有在compose.yml文件中定义的容器服务
```

```
## 6).测试访问
http://192.168.149.135/hello
```