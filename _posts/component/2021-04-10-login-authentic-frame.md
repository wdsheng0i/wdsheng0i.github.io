---
layout: post
title: 登录鉴权方式、鉴权框架
category: component
tags: [ component ]
---

登录鉴权方式、鉴权框架

## 参考资料

[《Apache Shiro 参考手册》](https://waylau.gitbooks.io/apache-shiro-1-2-x-reference/content/  )

在网站中，http请求是无状态的。即使第一次和服务器连接后并且登录成功后，第二次请求服务器依然不能知道当前请求是哪个用户。cookie的出现就是为了解决这个问题

session的出现，是为了解决cookie存储数据不安全的问题的。

## 一、session和cookies的区别

- 1.cookie是一种发送到客户浏览器的文本串句柄，并保存在客户机硬盘上，可以用来在某个WEB站点会话间持久的保持数据。
    - 数量受到限制。一个浏览器能创建的 Cookie 数量最多为 300 个，并且每个不能超过 4KB，每个 Web 站点能设置的 Cookie 总数不能超过
      20 个
    - 浏览器可以禁用Cookie，禁用Cookie后，也就无法享有Cookie带来的方便。
    - 不安全，受到跨站点脚本攻击时，脚本指令将会读取当前站点的所有 Cookie 内容（已不存在 Cookie 作用域限制），然后通过某种方式将
      Cookie 内容提交到指定的服务器（如：AJAX）
- 2.session：会话控制，其实指的就是访问者从到达某个特定主页到离开为止的那段时间。
    - 安全性上来讲，Session比Cookie安全性稍微高一些，服务器会为客户端创建一个Session，并将通过特殊算法算出一个session的ID
    - 因为Session是存储在服务器当中的，所以Session过多，会对服务器产生压力。在我看来，Session的生命周期算是减少服务器压力的一种方式。
- 3.cookie和session的共同之处在于：cookie和session都是用来跟踪浏览器用户身份的会话方式。
- 4.cookie和session的区别是：cookie数据保存在客户端，session数据保存在服务器端。

```
简单的说，当你登录一个网站的时候，
如果web服务器端使用的是session，那么所有的数据都保存在服务器上，客户端每次请求服务器的时候会发送当前会话的sessionid，服务器根据当前sessionid判断相应的用户数据标志，以确定用户是否登录或具有某种权限。
由于数据是存储在服务器上面，所以你不能伪造，但是如果你能够获取某个登录用户的 sessionid，用特殊的浏览器伪造该用户的请求也是能够成功的。
sessionid是服务器和客户端链接时候随机分配的，一般来说是不会有重复，但如果有大量的并发请求，也不是没有重复的可能性.

如果浏览器使用的是cookie，那么所有的数据都保存在浏览器端，比如你登录以后，服务器设置了cookie用户名，那么当你再次请求服务器的时候，浏览器会将用户名一块发送给服务器，这些变量有一定的特殊标记。
服务器会解释为cookie变量，所以只要不关闭浏览器，那么cookie变量一直是有效的，所以能够保证长时间不掉线。
如果你能够截获某个用户的 cookie变量，然后伪造一个数据包发送过去，那么服务器还是认为你是合法的。所以，使用 cookie被攻击的可能性比较大。
如果设置了的有效时间，那么它会将cookie保存在客户端的硬盘上，下次再访问该网站的时候，浏览器先检查有没有 cookie，如果有的话，就读取该 cookie，然后发送给服务器。
如果你在机器上面保存了某个论坛cookie，有效期是一年，如果有人入侵你的机器，将你的cookie拷走，然后放在他的浏览器的目录下面，那么他登录该网站的时候就是用你的的身份登录的。
所以 cookie是可以伪造的。当然，伪造的时候需要主意，直接copy cookie文件到cookie目录，浏览器是不认的，他有一个index.dat文件，存储了cookie文件的建立时间，以及是否有修改，所以你必须先要有该网站的 cookie文件，并且要从保证时间上骗过浏览器
```

- 5.两个都可以用来存私密的东西，同样也都有有效期的说法,区别在于session是放在服务器上的，过期与否取决于服务期的设定，cookie是存在客户端的，过期与否可以在cookie生成的时候设置进去。
    - (1)cookie数据存放在客户的浏览器上，session数据放在服务器上
    - (2)cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗,如果主要考虑到安全应当使用session
    - (3)session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，如果主要考虑到减轻服务器性能方面，应当使用COOKIE
    - (4)单个cookie在客户端的限制是3K，就是说一个站点在客户端存放的COOKIE不能3K。
    - (5)所以：将登陆信息等重要信息存放为SESSION;其他信息如果需要保留，可以放在COOKIE中

## 二、Token

- [傻傻分不清之 Cookie、Session、Token、JWT](https://juejin.im/post/6844904034181070861)
- [sa-token](https://github.com/click33/sa-token)

## 三、JWT

[深入理解Spring Cloud Security OAuth2及JWT](https://www.jianshu.com/p/cb886f995e86)

## 四、OAuth2

## 五、spring-session

## 六、鉴权框架Shiro

### 1.Shiro介绍

Apache Shiro是Java的一个安全框架。可能没有Spring Security做的功能强大，但是在实际工作时可能小而简单的Shiro就足够了。

Shiro可以非常容易的开发出足够好的应用，不仅可以用在JavaSE环境，也可以用在JavaEE环境。Shiro可以帮助我们完成：认证、授权、加密、会话管理、与Web集成、缓存等。

![](https://waylau.gitbooks.io/apache-shiro-1-2-x-reference/content/images/ShiroFeatures.png)

### 2.Shiro应用安全的四大基石

- Authentication（认证）：用户身份识别，通常被称为用户“登录”
- Authorization（授权）：访问控制。比如某个用户是否具有某个操作的使用权限。
- Session Management（会话管理）：特定于用户的会话管理,甚至在非web 或 EJB 应用程序。
- Cryptography（加密）：在对数据源使用加密算法加密的同时，保证易于使用。

还有其他的功能来支持和加强这些不同应用环境下安全领域的关注点。特别是对以下的功能支持：

- Web支持：Shiro 提供的 Web 支持 api ，可以很轻松的保护 Web 应用程序的安全。
- 缓存：缓存是 Apache Shiro 保证安全操作快速、高效的重要手段。
- 并发：Apache Shiro 支持多线程应用程序的并发特性。
- 测试：支持单元测试和集成测试，确保代码和预想的一样安全。
- “Remember Me”：跨 session 记录用户的身份，只有在强制需要时才需要登录。
- “Run As”：这个功能允许用户假设另一个用户的身份(在许可的前提下)。

**高级概述**  
在概念层，Shiro 架构包含三个主要的理念：Subject,SecurityManager和
Realm。下面的图展示了这些组件如何相互作用，我们将在下面依次对其进行描述。  
![](https://waylau.gitbooks.io/apache-shiro-1-2-x-reference/content/images/ShiroBasicArchitecture.png)

- Subject: 本质上是当前运行用户特定的'View'(视图)，而单词“User”经常暗指一个人，Subject
  可以是一个人，但也可以是第三方服务、守护进程帐户、时钟守护任务或者其它--当前和软件交互的任何事件。 Subject 实例都和（也需要）一个
  SecurityManager 绑定，当你和一个Subject 进行交互，这些交互动作被转换成 SecurityManager 下Subject 特定的交互动作。
- SecurityManager:  是 Shiro 架构的核心，安全管理器，管理所有Subject，可以配合内部安全组件。(
  类似于SpringMVC中的DispatcherServlet)。
- Realms：是 Shiro 和你的程序安全数据之间的“桥”或者“连接”
    - 它用来实际和安全相关的数据如用户执行身份认证（登录）的帐号和授权（访问控制）进行交互， Realm 本质上是一个特定的安全
      DAO：它封装与数据源连接的细节，得到Shiro 所需的相关的数据。
    - 在配置Shiro的时候，你必须指定至少一个Realm来实现认证（authentication）和/或授权（authorization）
    - SecurityManager 可以配置多个复杂的 Realm，但是至少有一个是需要的。
    - Shiro 提供开箱即用的 Realms来连接安全数据源（或叫地址）如LDAP、JDBC、文件配置如INI和属性文件等，如果已有的Realm不能满足你的需求你也可以开发自己的Realm实现。
    - 和其它内部组件一样，Shiro SecurityManager 管理如何使用 Realms 获取 Subject 实例所代表的安全和身份信息。

### 3. 身份认证流程Authentication

principals: 身份，如（用户名，邮箱，手机号） 唯一即可

credentials: /凭证 如密码 ，安全证书  
![](https://images2018.cnblogs.com/blog/1361523/201803/1361523-20180327124333666-1955995367.png)

- 1.首先通过 subject.login 进行登录 ，之前必须设置 SecurityUtils.setSecurityManager(securityManager)
- 2.Security Manager 负责身份验证的业务逻辑，他会委托给Authenticator （验证器） 进行验证
- 3.Authenticator 身份验证，此处可以插入自己的实现
- 4.Authenticator 可能会委托给相应的 AuthenticationStrategy （认证策略） 进行多Realm身份验证，默认
  ModularRealmAuthenticator 会调用 AuthenticationStrategy 进行验证
- 5.Authenticator 会把相应的Token传入Realm，从Realm中获取身份验证信息。 如果没有返回/抛出异常，代码
  验证失败（可以配置多个Realm，按照配置策略循序访问）

### 4.授权流程Authorization

![](https://images2018.cnblogs.com/blog/1361523/201803/1361523-20180329102624334-370810485.png)

- Authorizer 负责授权（访问控制）
- PermissionResolver 用于解析字符串得到Permission实例
- RolePermissionResolver 用于根据角色解析相应的权限集合

### 5.加解密Cryptography

为了方便使用

- Shiro提供了HashService，默认提供了DefaultHashService实现, 使用散列算法
- Shiro提供了PasswordService 及 PasswordMatcher 用于加密和验证密码服务  
  PasswordMatcher，其是一个CredentialsMatcher实现；

将credentialsMatcher赋值给myRealm，myRealm间接继承了AuthenticatingRealm，其在调用getAuthenticationInfo方法获取到AuthenticationInfo信息后，会使用credentialsMatcher来验证凭据是否匹配，如果不匹配将抛出IncorrectCredentialsException异常。

```
String str = "hello";
String salt = "1234";String pubSalt = "123456";
        
DefaultHashService hashService = new DefaultHashService(); //默认算法SHA-512
hashService.setPrivateSalt(new SimpleByteSource(salt)); //设置私盐, 会与传入的公盐产生一个新盐
hashService.setGeneratePublicSalt(true);// 判断 在没有传入公盐的时候 自动生成个公盐
hashService.setRandomNumberGenerator(new SecureRandomNumberGenerator()); //生成公盐     

//需要构建一个HashRequest，传入算法、数据、公盐、迭代次数   
HashRequest request = new HashRequest.Builder()
     .setAlgorithmName("MD5").setSource(ByteSource.Util.bytes(str))
     .setSalt(ByteSource.Util.bytes(pubSalt)).setIterations(2).build();     //设计迭代次数  （加密多少次）  
        
String hex = hashService.computeHash(request).toHex();
```

AES

```
AesCipherService aesCipherService = new AesCipherService();
aesCipherService.setKeySize(128); //设置长度
//生成key
Key key = aesCipherService.generateNewKey();
String test = "hello";//加密     
String encrptText = aesCipherService.encrypt(test.getBytes(), key.getEncoded()).toHex();     
//解密   
String text2 = CodecSupport.toString(aesCipherService.decrypt(Hex.decode(encrptText), key.getEncoded()).getBytes());  
```

### 6.Session管理

会话管理器管理着应用中所有Subject的会话的创建、维护、删除、失效、验证等工作。  
是Shiro的核心组件，顶层组件SecurityManager直接继承了SessionManager，且提供了SessionsSecurityManager实现直接把会话管理委托给相应的SessionManager，DefaultSecurityManager及DefaultWebSecurityManager默认SecurityManager都继承了SessionsSecurityManager。

```
1.自定义SessionDao 持久化session 相关信息

2.配置SessionManager
```

### 记住我rememberMe

### 缓存caching

### springboot整合shiro

Spring Boot 中集成 Shiro https://blog.csdn.net/taojin12/article/details/88343990    
https://blog.csdn.net/wanliangsoft/article/details/86533754

- 《springboot与shiro整合》：https://www.cnblogs.com/zls1218/tag/shiro/
- springboot与shiro整合 ：https://www.cnblogs.com/ljk1107/p/13673569.html
- springboot与shiro整合(示例) https://blog.csdn.net/u014203449/category_7482067.html
- springboot整合shiro（完整版） https://www.jianshu.com/p/7f724bec3dc3
- springboot+shiro整合教程 https://www.cnblogs.com/007sx/p/9603712.html
- Spring Boot (十四)： Spring Boot 整合
  Shiro-登录认证和权限管理 http://www.ityouknow.com/springboot/2017/06/26/spring-boot-shiro.html

## 七、鉴权框架Spring Security
 


