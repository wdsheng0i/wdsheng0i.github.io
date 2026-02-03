---
layout: post
title: 服务网关 spring cloud zuul
category: springcloud
tags: [ springcloud ]
---

Zuul 是在云平台上提供动态路由,监控,弹性,安全等边缘服务的框架。

## 参考资料

- http://www.ityouknow.com/springcloud/2017/06/01/gateway-service-zuul.html
- http://www.ityouknow.com/springcloud/2018/01/20/spring-cloud-zuul.html
- https://www.fangzhipeng.com/springcloud/2017/06/05/sc05-zuul.html

## 问题总结

- [SpringBoot上传文件，经过spingCloud-Zuul，中文文件名乱码解决办法](https://www.cnblogs.com/zincredible/p/9268783.html)
- [SpringBoot上传文件，经过spingCloud-Zuul，中文文件名乱码解决办法](https://blog.csdn.net/arjg30483/article/details/102325007)
- [SpringCloud-MessageConverter上传文件出现中文乱码的问题及解决方案](https://blog.csdn.net/qq_38927057/article/details/81209421)

```
乱码请求：http://ip:port/demo/demo-mgmt/chm/uploadChmFile 
解决乱码：http://ip:port/demo/zuul/demo-mgmt/chm/uploadChmFile
```