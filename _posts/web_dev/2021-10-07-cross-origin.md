---
layout: post
title: 跨域问题
category: web
tags: [web]
---

跨域问题

## 参考资料 
- [跨域问题：解决跨域的三种方案](https://www.cnblogs.com/itmacy/p/6958181.html)

## 一、为什么会产生跨域问题 
跨域，指的是浏览器不能执行其他网站的脚本。    
它是由浏览器的同源策略造成的，是浏览器施加的安全限制    
所谓同源是指，域名，协议，端口均相同，不明白没关系，举个栗子：
```
http://www.123.com/index.html 调用 http://www.123.com/server.php （非跨域）
http://www.123.com/index.html 调用 http://www.456.com/server.php （主域名不同:123/456，跨域）
http://abc.123.com/index.html 调用 http://def.123.com/server.php （子域名不同:abc/def，跨域） 
http://www.123.com:8080/index.html 调用 http://www.123.com:8081/server.php （端口不同:8080/8081，跨域）
http://www.123.com/index.html 调用 https://www.123.com/server.php （协议不同:http/https，跨域） 
```
请注意：localhost和127.0.0.1虽然都指向本机，但也属于跨域。   
浏览器执行javascript脚本时，会检查这个脚本属于哪个页面，如果不是同源页面，就不会被执行。
 
- 1、浏览器限制  
- 2、跨域（域名，端口不一样都是跨域）  
- 3、XHR（XMLHttpRequest请求）  

同时满足三个条件才有可能产生跨域问题。  

## 二、解决跨域问题方案。
### 2.1.从浏览器出发，允许浏览器跨域。

### 2.2.从XHR（XMLHttpRequest）出发避免发生跨域
使用jsonp，由于jsonp请求是通过script的方式发送的（只有xhr的请求方式才有可能产生跨域问题），所以不会产生跨域问题。    
Spring MVC实现过程：前台使用ajax的get请求，将dataType设为“jsonp”；服务器创建一个类并继承抽象类AbstractJsonReponseBodyAdvice,最后注解@ControllerAdivece。  
使用jsonp的弊端，只能使用get方式请求，服务器需要改动代码，发送的不是xhr请求。    

### 2.3.产生跨域后解决。
从被调用方考虑，有三种情况，分别是服务器实现，nginx配置和apache配置。  

#### 2.3.1 服务器设置Access-Control-Allow-Origin
需要注意两种情况，简单请求和非简单请求。  
简单请求是先执行请求再验证，非简单请求是先验证再请求。
- 简单请求（比较常见）：方法为get，head，post,请求header里面没有自定义头，Content-Type的值为以下几种 text/plain,multipart/form-data,application/x-www-form-urlencoded。
- 非简单请求（比较常见）：put,delect方法的ajax请求，发送json格式的ajax请求，带自定义头的ajax请求。

简单请求处理方案：在响应头中添加
- Access-Control-Allow-Origin=“允许跨域的url”，即跨省域时，请求头Origin的值，所以一般是获取Origin的值。
- Access-Control-Allow-Method=“*”，允许的方法。

非简单请求处理方案：在相应头中添加
- Access-Control-Allow-Origin=“允许跨域的url”，即跨域时，可以获取请求头Origin的值。
- Access-Control-Allow-Method=“*”，允许的方法
- Access-Control-Request-Headers=“Content-Type,自定义的header的key”。

带cookies的跨域解决：在响应头添加
- Access-Control-Allow-Credentials,="true"，允许使用cookies

#### 2.3.2 ngnix配置 
用nginx的反向代理机制解决前端跨域问题  
http://www.cnblogs.com/gabrielchen/p/5066120.html


#### 2.3.3 spring框架：使用@CrossOrigin注解既可以实现服务端跨域
```
// 允许跨域调用
@CrossOrigin(origins = "*", maxAge = 3600, allowCredentials="true")
public class CyryController {
```