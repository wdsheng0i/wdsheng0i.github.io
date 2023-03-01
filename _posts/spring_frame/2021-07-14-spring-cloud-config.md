---
layout: post
title: 服务配置中心：apollo
category: springcloud
tags: [springcloud]
---

服务配置中心：apollo

## 参考资料
- [apollo介绍-github](https://github.com/ctripcorp/apollo/wiki)
- [官方文档](https://www.apolloconfig.com/#/zh/design/apollo-introduction)

## 一、apollo配置中心

### 1.1 apollo部署、配置
[分布式部署指南](https://www.apolloconfig.com/#/zh/deployment/distributed-deployment-guide)  

【组件部署】Apollo
![](../../assets/images/2021/apollo/apollo.png)

``` 
安装包：https://github.com/apolloconfig/apollo/releases  

主机规划
192.168.145.195 apollo-portal apollo-config  
192.168.145.228 apollo-admin apollo-config  

1、导入数据库初始化文件（此sql已包含创建数据库的步骤）
MySQL > source /your_local_path/apolloconfigdb.sql
MySQL > source /your_local_path/apolloportaldb.sql

2、配置Eureka
UPDATE `ApolloConfigDB`.`ServerConfig` 
SET `Value` = 'http://testuser:1qaz@WSX@192.168.145.195:11000/eureka/,
http://testuser:1qaz@WSX@192.168.145.228:11000/eureka/'
WHERE `Key` = 'eureka.service.url';

3、配置环境列表与组织名称
UPDATE `ApolloPortalDB`.`ServerConfig` 
SET `Value` = 'pro' 
WHERE `Key` = 'apollo.portal.envs';
UPDATE `ApolloPortalDB`.`ServerConfig` 
SET `Value` = '[{"orgId":"WDSSOFT","orgName":""}]' 
WHERE `Key` = 'organizations';
 
4、配置数据库连接文件
# apollo-adminservice/config/application-github.properties
# apollo-configservice/config/application-github.properties
spring.datasource.url = jdbc:mysql://192.168.145.195:3306/apolloconfigdb?
characterEncoding=utf8&useLocalSessionState=true
spring.datasource.username = user
spring.datasource.password = password

# apollo-portal/config/application-github.properties
spring.datasource.url = jdbc:mysql://192.168.145.195/apolloportaldb?
characterEncoding=utf8&useLocalSessionState=true
spring.datasource.username = user
spring.datasource.password = password

5、配置环境的meta server地址
# apollo-portal/config/apollo-env.properties
lpt.meta=${lpt_meta}
pro.meta=http://192.168.145.195:9000,http://192.168.145.228:9000

6、修改apollo-admin、apollo-config的Eureka注册id
# apollo-config/scripts/startup.sh
# apollo-admin/scripts/startup.sh
export JAVA_OPTS="$JAVA_OPTS -Deureka.instance.instance-id=192.168.145.195:$SERVER_PORT ......

7、配置日志位置
# apollo-config/scripts/startup.sh
# apollo-admin/scripts/startup.sh
# apollo-portal/scripts/startup.sh
## Adjust log dir if necessary
LOG_DIR=./logs/100003172

8、依次启动apollo-config、apollo-admin、apollo-portal
/bin/bash apollo-config/scripts/startup.sh
/bin/bash apollo-admin/scripts/startup.sh
/bin/bash apollo-portal/scripts/startup.sh
```

### 1.2 springboot集成使用apollo
- [SpringBoot 整合 apollo](https://www.cnblogs.com/huanchupkblog/p/10509427.html)  
- [Spring Boot集成apollo配置中心](https://blog.csdn.net/lovelichao12/article/details/81013257)  
- [一文搞定：SpringBoot 集成 Apollo 配置中心](https://blog.csdn.net/qq_42046105/article/details/105108610)

## 二、springcloud-config配置中心
俗称的配置中心，配置管理工具包，让你可以把配置放到远程服务器，集中化管理集群配置，目前支持本地存储、Git以及Subversion。


  