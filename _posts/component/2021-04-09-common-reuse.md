---
layout: post
title: 公共服务（组件）：可复用基础公共能力建设总结
category: component
tags: [component]
---

可复用基础组件能力建设总结

## 参考资料 
系统基础功能、能力沉淀复用出发点：    
- 复用基础功能,避免重复造轮子,提升效率；
- 使业务系统专注于业务研发；
- 解决项目数据共享问题；

## 1 系统基础功能（含统一身份认证服务uim-server）
- 组织机构管理（公司、部门）
- 角色管理
- 用户管理
- 权限(菜单)管理
- 岗位管理


- 字典管理
    - 字典类型
    - 字典数据
- 日志管理
    - 操作日志
    - 登陆日志
- 通知公告
    - 门户公告
    - 短信公告
- 服务监控
    - 在线用户：（批量）强制退出
    - 服务器监控：cpu、内存、磁盘、jvm、os

## 2 单点登陆(CAS)服务 sso-server
- https://blog.csdn.net/qq1031893936/article/details/81190463
- https://blog.csdn.net/netsec_steven/article/details/106230338

## 3 存储服务 Minio、sso 
fastdfs也满足实际使用，对比下seaweedfs、oss

## 4 文件转换服务 （word excel pdf html 预览 编辑 及文件转换）
根据需求是否需要，部分在线编辑功能可能需要付费插件支持

## 5 短信服务
短信公告、短信通知

## 6 消息推送服务
门户公告