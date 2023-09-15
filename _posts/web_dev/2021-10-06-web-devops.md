---
layout: post
title: 前端开发工程化
category: web
tags: [web]
---

前端开发工程化

## 参考资料

## 一、构建 webpack-grunt

## 二、IDE工具 vscode-webstom

## 三、部署 nginx

## 四、依赖管理 npm

## 五、模块化 requirejs-es6-AMD-CMD

## 六、编码规范 Eslint

## 七、接口模拟 moco
- https://www.cnblogs.com/eastonliu/p/10186867.html  
- https://blog.csdn.net/qq_32706349/article/details/80472445  
- https://www.cnblogs.com/tangqiu/p/9493147.html  

### 7.1 moco的应用场景
在开发过程中会经常使用到一些http网络接口，通常是第三方或者说其他团队或者是由后端同事开发的，此时不能提供服务。  
要集成服务时，依赖的服务还不存在，此时影响开发和测试
通常做法  
搭一套Web服务(缺点:耗时费力)  
基于以上情况，Moco或者与Moco类似的工具出现  

### 7.2 Moco 原理:
Moco会根据一些配置，启动一个真实的HTTP服务，当发起的请求满足某一个条件时，就会收到一个应答。Moco底层没有依赖于像Servlt这样的大的框架，而是基于Netty的网络应用框架编写而成,这样就绕过了复杂的应用服务器，所以速度来讲是很快的。  

### 7.3 Moco的优点：  
- 配置简单，简易服务，配置reques，response等就可以满足需求，支持http，https，socket。
- 支持在request钟设置headers，Cookies，statuscode等  
- 对GET,POST,PUT,DELET请求方式均支持，适合web开发  
- 无须复杂的环境配置  
- 修改配置后，可立即生效，只需要维护接口，即锲约即可。  
- 对可能用到的数据格式都支持，如json，text，xml，file等  
- 还能与其他工具集成

### 7.4 安装步骤
- 下载地址:https://github.com/dreamhead/moco
- 下载moco-runner--standalone.jar 包
- 创建Json配置文件
- 启动: 执行命令 java -jar moco-runner--standalone.jar <协议类型（http）> -p <端口号> -c - Json配置文件

### 7.5 moco示例
```
1、约定请求:
[{"request":{"uri": "/"},"response": {"text" : "Hello java engineers "}}]
效果预览在浏览器输入:http://localhost:[port]

2、 约定queries
[{"request":{"uri": "/test","queries": {"systemid" : "dsjglpt"}},"response": {"text" : "欢迎来到大数据管理平台系统 "}}]
效果预览在浏览器输入:http://localhost:[port]/test?systemid=dsjglpt

3、 约定请求method
[{"request":{"method" : "get","uri": "/test",},"response": {"text" : "欢迎来到大数据管理平台系统 "}}]

4、约定响应 status
[{"request":{"uri": "/test",},"response": {"status" : 200}}]
```

## 八、运行环境 nodejs
### 8.1 [Node.js对于Java开发者而言是什么](https://www.smsyun.cc/Index/News/main/id/2239.html)
- Node.js是一个Javascript运行环境(runtime)。实际上它是对Google V8引擎进行了封装。      
- V8引 擎执行Javascript的速度非常快，性能非常好。      
- Node.js对一些特殊用例进行了优化，提供了替代的API，使得V8在非浏览器环境下运行得更好。    
- Node.js是一个基于Chrome JavaScript运行时建立的平台，用于方便地搭建响应速度快、易于扩展的网络应用。    
- Node.js 使用事件驱动， 非阻塞I/O模型而得以轻量和高效，非常适合在分布式设备上运行数据密集型的实时应用。

### 8.2 下载安装:[Node.js 中文网](http://nodejs.cn/)  

## 九、模拟静态服务器 sublimeServer
Sublime Text的服务器插件：SublimeServer。
  
使用了该插件后，不在需要单独的启动Tomcat或者Apache这样的重型服务器，就可以完成HTML和Java在服务器的联调。
SublimeServer会启动一个轻量级的，静态的WEB服务器，让你在文本编辑器中直接启动服务器，并进行测试联调。

安装方法：  
``` 
1.打开Package Control，选择install package：输入sublime serve就行了，安装完成

2.使用sublime server插件  
点击tool–sublimeserver–start sublimeserver
 
3.sublime打开web文件夹，然后使用sublime打开html页面，右键选择view in sublimeserve
```

注：（在setting里面可以修改服务器的端口号，如8090） 

## 十、转换器 babel-traceur










