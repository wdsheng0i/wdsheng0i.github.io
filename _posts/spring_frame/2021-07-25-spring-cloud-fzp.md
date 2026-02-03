---
layout: post
title: 《深入理解spring cloud与微服务构建》-方志朋
category: springcloud
tags: [ springcloud ]
---

## 参考资料

[《深入理解spring cloud与微服务构建》-方志朋](https://www.fangzhipeng.com/spring-cloud.html)

## 第1章 微服务简介

### 1.1 单体架构及其存在的不足

#### 1.1.2 单体架构存在的不足：

- 业务越复杂，单体应用的代码量越大，代码的可读性、可维护性和可扩展性下降
- 随着用户越来越多，程序承受的并发越来越高，单体应用的并发能力有限，业务扩展受限
- 单体应用的业务都在同1个程序中，随着业务的扩张、复杂度的增加, 测试的难度越来越大

#### 1.1.3 单体架构使用服务器集群部署

![单体架构使用服务器集群部署](https://wdsheng0i.github.io/assets/images/2021/micro/micro6.png)

> 负载均衡服务器 + 应用服务器集群 + 缓存服务器集群 + 文件服务器 + 数据库读写分离

- 优点：
    - 有一定的并发能力，能应对一定的复杂业务，改善了系统性能.
- 不足：
    - 有大量的代码，代码的可读性和可维护性依然很差。
    - 面对海量的用户，数据库将会成为瓶颈，解决方案将使用分布式数据库，也就是将数据库进行分库分表
    - 持续交付能力差，业务越复杂，代码越 ，修改代码和添加代码所 的时间越长

### 1.2 微服务

#### 1.2.1 微服务定义

微服务架构的风格，就是将单一程序开发成一个微服务，每个微服务运行在自己的进程中，并使用轻量级机制通信，通常是 HTTP RESTFUL
API 。这些服务（独立运行的程序，即服务单元）围绕业务能力来划分构建的，并通过完全自动化部署机制来独立部署。这些服务可以使用不同的编程语言，以及不同数据存储技术，以保证最低限度的集中式管理。

以我个人对这段话的理解，总结微服务具有如下特点。

- 按业务划分为一个独立运行的程序，即服务单元。
    - 高度组件化的模块、
    - 稳定的边界、
    - 服务间无耦合、
    - 扩展性/复用性好
- 服务之间通过 HTTP 协议相互通信。
    - 请求、处理业务逻辑、返回数据的 HTTP 模式非常高效，
    - 并且这种通通信机制与平台和语言无关。
    - 服务间也可通过轻量级的消息总线来通信，例如 RabbitMQ Kafaka 等。
    - 服务通信的数据格式一般为JSON、XML,这两种数据格式与语言、平台、通信协议无关
- 自动化部署: gitlab + Jenkins + maven + docker
- 可根据服务单元的功能，用不同的编程语言实现。
- 可以用不同的存储技术。
    - 服务数据库独立：单服务数据量少、易维护、性能利用好、迁移方便
    - 数据存储方式可以不同,可按需选择: Mysql、Redis、MongoDB ...
- 服务集中化管理（注册和发现服务）。
    - spring cloud 采用 Eureka
    - Zookeeper
    - Consul
- 微服务是 个分布式系统。
    - 更复杂：服务独立性、调用可靠性
    - 数据一致性问题：分布式事务、全局ID、分布式锁
    - 网络依赖：服务间通信对网络稳定性要求高

#### 1.2.2 微服务的优势

- 服务拆分，复杂问题简单化，代码可读性、可扩展性增加
- 分布式集群化部署，具有很强的横向扩展能力
- 服务单元间完全独立，无耦合，通过HTTP请求通信，开发新服务单元不再受限于旧功能的技术选型
- 微服务重构某个服务单元难度远小于单体架构重构整个项目
- 修改功能，只需要测试和部署某个独立部署的服务单元
- 高可用服务器集群提高负载能力，分区容错使系统更健壮

### 1.3 微服务的不足

#### 1.3.1 微服务复杂度

分布式系统构建的复杂度、框架知识和架构知识的学习、服务间的通信、服务间的依赖

#### 1.3.2 分布式事务

> CAP理论

- Consistency ：指数据的强一致性。如果写入某个数据成功，之后读取，读到的都是新写入的数据：如果写入失败，之后读取的都不是写入失败的数据。
- Availability ：指服务的可用性
- Partition-tolerance ：指分区容错  
  单体架构服务是CA系统；微服务系统通常是AP系统（即同时满足可用性和分区容错性）

![](https://wdsheng0i.github.io/assets/images/2021/micro/micro1.png)

> 分布式系统中如何保证数据一致性？使用分布式事务（两阶段提交）

- 第一阶段：
    - A发起事务并交给事务协调器TC处理
    - TC向所有参与事务的节点发送处理事务操作的准备操作
    - 节点执行准备操作，将Undo Redo 信息写进日志，并向事务管理器返回准备操作是否成功
- 第二阶段：
    - 事务管理器收集所有节点的准备操作是否成功，如果都成功，则通知所有的节点执行提交操作；如果有 个失败，则执行回滚操作。

#### 1.3.3 服务划分

#### 1.3.4 服务部署

Docker容器部署微服务最佳实践

### 1.4 微服务和SOA的关系

SOA：即面向服务的架构  
ESB：企业服务总线  
微服务：一种更轻便敏捷的SOA的实现

### 1.5 微服务的设计原则

> 软件设计应该是渐进式发展，根据业务需求选择：

- 业务需求量小->单体架构
- 业务扩展，用户增加->(负载均衡服务器 + 应用服务器集群 + 缓存 + 数据库读写分离)
- 业务继续扩展发展->微服务架构

> 微服务设计的三大难题：

- 服务故障传播性：容错、熔断机制组件
- 服务的划分：根据业务划分服务，领域驱动设计具有指导作用
- 分布式事务：两阶段、三阶段提交(事务失败时，人工恢复数据)

## 第2章 Spring Cloud简介

### 2.1 微服务应该具备的功能

#### 2.1.1 服务注册与发现(Eureka、zookeeper)

- 角色：服务注册中心、服务提供者、服务消费者
- 服务注册：指向服务注册中心注册1个服务实例，将服务提供者信息（如服务名、 IP地址等〉告知注册中心
- 服务发现：指当服务消费者需要消费另外1个服务时，服务注册中心告知服务消费者它所要消费服务的实例信息（如服务名、 地址等〉。
- 服务消费：服务消费者一般使用 HTTP 协议或者消息组件这种轻量级的通信机制来进行服务消费。
- 健康检查：通常一个服务实例注册后，会定时向服务注册中心提供“心跳”，以表明自己还处于可用的状态

#### 2.1.2 服务的负载均衡(Ribbon、Nginx、LVS、F5)

每个服务都会从注册中心同步服务列表，服务消费者集成负载均衡组件；  
当服务消费者消费服务时，负载均衡组件获取服务提供者所有实例的注册信息，并通过一定的负载均衡策略（开发者可以配置），选择一个服务提供者的实例
，向该实例进行服务消费，这样就实现了负载均衡。

#### 2.1.3 服务的容错（熔断、降级、限流）(Hystix)

当网络延迟或者故障等因素导致某个服务无响应，高并发下会导致服务器线程资源耗尽，造成服务瘫痪，即雪崩效应
> 熔断器机制

![](https://wdsheng0i.github.io/assets/images/2021/micro/micro2.png)

> 熔断器机制作用

- 资源隔离：问题API隔离，防止请求阻塞
- 服务降级：短时大量请求，熔断打开，防止服务器故障
- 自我检测、监控、修复：短时网络故障导致服务不可用，半打开状态尝试成功后恢复服务可用

#### 2.1.4 服务网关(Zuul、Nginx)

微服务系统通过将资源以 API接口的形式暴露给外界来提供服务。在微服务系统中，API接口资源通常是由服务网关（也称 API
关）统一暴露，内部服务不直接对外提供API资源的暴露  
![](https://wdsheng0i.github.io/assets/images/2021/micro/micro3.png)

1) 网关层需要做到高可用，一般以集群的形式存在。
2) 在服务网关层之前，有可能需要加上负载均衡层，通常为Nginx双机热备，通过一定的路由策略将请求转发到网关层。
2) 经过网关层一系列的用户身份验证、权限判断 最终转发到具体的服务。

> 网关层具有很重要的意义

- 网关将所有服务的API接口资源统一聚合，对外统一暴露，保护了内部微服务单元的 API 接口，防止被外界调用以及服务的敏感息对外暴露
- 可以做些 用户身份认证、权限认证，防止非法请求操作API接口，对内部服务起到保护作用
- 网关可以实现监控功能，实时日志输出，对请求进行记录
- 可以用来做流量监控，在高流量的情况下，对服务进行降级
- API 接口从内部服务分离出 ，方便做测试

#### 2.1.5 服务配置的统一管理(SpringCloudConfig、Apollo)

服务配置的统一管理大致过程如下

- 首先onfig Server （配置服务〉读取配置文件仓库的配置信息，其中配置文件仓库可以存放在配置服务的本地仓库，也可以放在远程的
  Git 仓库（例如 GitHub oding 等〉。
- 配置服务启动后，读取配置文件信息，读取完成的配置信息存放在配置服务的内存中。
- 当启动服务A、B时，由于服务A、B指定了向配置服务读取配置信息，服向配置服务读取配置信息。
- 当服务的配置信息需要修改且修改完成后，向配置服务发送Post请求进行刷新，这时服务A、B会向配置服务重写读取配置文件。  
  ![](https://wdsheng0i.github.io/assets/images/2021/micro/micro4.png)      
  如果服务数量较多，对配置中心需要考虑集群化部署，从而使配置中心高可用，做分布式集群

#### 2.1.6 服务链路追踪(Dapper、Zipkin、Eagleeye、SpringCloudSleuth)

将请求过程的数据记录，去跟进一个请求有哪些服务参与，参与的顺序是怎样的 ，从而使每个请求链路清晰可见，出了问题很快就能定位。

#### 2.1.7 微服务监控(SpringCloudAdmin)

#### 2.1.8 微服务调用(Feign、Dubbo)

### 2.2 Spring Cloud简介

Spring Cloud 是基于 Spring Boot的    
Spring Cloud 的首要目标是通过提供一系列开发组件和框架，帮助开发者迅速搭建分布式的微服务系统    
Spring Cloud 是通过包装其他技术框架来实现的。     
![](https://wdsheng0i.github.io/assets/images/2021/micro/cloud.png)

> 常用组件

- SpringCloudNetflix ：包装了Netflix公司的微服务组件实现的，是Spring Cloud 核心的核心组件，包括 Eureka Hystrix Zuul
  Archaius 等。
- **Eureka**：服务注册和发现组件
- **Hystrix**：熔断器组件 Hystrix 防止微服务系统发生雪崩效应。限流、降级、监控
- **Zuul**：网关组件，智能路由、请求过滤、接口统一暴露、安全验证、权限控制
- **Ribbon**：负载均衡组件。
- **Feign**：明式远程调度组件
- **Spring Cloud Bus**：消息总线组件，和Spring Cloud Config 配合，用于动态更新服务的配置。
- **Spring Cloud Sleuth**：服务链路追踪组件，封装了Dapper Zipkin, Kibina 等组件，实时监控服务的链路调用。
- **Spring Cloud Config**：服务配置中心组件
- **Spring Cloud Stream**：数据流操作组件，可以封装 Redis RabbitMQ Kafka 等组件实现发送和接收消息等。
- Archaius ：配置管理 API 的组件，主要用于多配置的动态获取。
- Spring Cloud Security 安全模块组件，是对 Spring Security封装，通常配合 0Auth2来保护微服务系统安全。
- Spring Cloud Consul ：Spring Cloud 对 Consul 的封装，和 Eureka 类似，是个服务注册和发现组件
- Spring Cloud Zookeeper：Spring Cloud Zookeeper 封装，用于服务的注册和发现
- Spring Cloud Data Flow ：大数据操作组件， Spring Cloud Data Flow SpringXD的替代品
- Spring Cloud CLI：Spring Cloud 对 Spring Boot CLI 的封装，以命令行方式快速运行和搭建容器
- Spring Cloud Task：基于 Spring Task ，提供了任务调度和任务管理的功能。
- Spring Cloud Connectors：用于 Paas 云平台连接到后端。

### 2.3 Dubbo简介

Dubbo是一个分布式服务框架；提供高性能和透明化的 RPC 远程服务调用方案，以及 SOA 服务冶理方案。

- RPC 远程调用：封装了长连接 NIO 框架，如 Netty Mina ，采用的是多线程模式。
- 集群容错：提供了基于接口方法的远程调用的功能 并实现了负载均衡策略、失败容错等功能。
- 服务发现：集成了 Apache Zookeeper 组件，用于服务的注册和发现

流程如下。  
![](https://wdsheng0i.github.io/assets/images/2021/micro/micro5.png)

- 服务提供者向服务中心注册服务
- 服务消费者订阅服务。
- 服务消费者发现服务。
- 服务消费者远程调度服务提供者进行服务消费，在调度过程中，使用了负载均衡策略、失败容错的功能。
- 服务消费者和提供者，在内存中记录服务的调用次数和调用时间，并定时每分钟发送1次统计数据到监控中心

特性

- 连通性：注册中心负责服务注册; 监控中心负责收集调用次数、调用时间; 注册中心、服务提供者、服务消费者为长连接。
- 健壮性：监控中心窑机不影响其他服务的使用：注册中心集群，任一个实例岩机自动切换到另一个注册中 实例 ：服务实例集群 任意 个实例
  机，自动切换到另外一个可用的实例
- 伸缩性：可以动态增减注册中心和服务的实例数量。
- 升级性：服务集群升级，不会对现有架构造成压力。

### 2.4 Spring Cloud与Dubbo比较

- Spring Cloud是一套微服务解决方案；Dubbo是优秀的服务治理和服务调用框架

### 2.5 K8s简介

Kubernetes 是一个容器集群管理系统，为容器化的应用程序提供部署运行、维护 扩展、资源调度 服务发现等功能。

### 2.6 Spring Cloud与K8s比较

Spring Cloud 是一个构建微服务的框架，通过众多的类库来实现微服务系统所需的各个组件，同时不断集成优秀的组件
Kubernetes 是通过对运行的容器的编排来实现构建微服务的  
两者从构建微服务的角度和实现方式有很大的不同，但它们提供了构建微服务
所需的全部功能

## 第3章 构建微服务准备

JDK、IDEA、MAVEN 安装、配置、使用

## 第4章 开发框架Spring Boot

主要的特点就是简化了开发和部署的过程，简化了Spring复杂的配置和依赖管理，通过起步依赖和内置 Servilet 容器能够使开发者迅速搭
Web 工程。

### 4.1 SpringBoot简介

> Springboot特点

- 自动配置、起步依赖 和 Actuator 运行状态的监控。

> Springboot优点

- 安全、度量、健康检查、内置Servlet容器 和 外置配置

### 4.3 Springboot配置文件详解

> 4.3.l 自定义属性

> 4.3.2 将配置文件的属性赋给实体类

> 4.3.3 自定义配置文件

> 4.3.4 多个环境的配置文件

### 4.4 运行状态监控 Springboot-Actuator

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### 4.S Spring Boot 整合 JPA （Mybatis）

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.2</version>
</dependency>

<!-- sql语句智能化 -->
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.0.0</version>
</dependency>
```

### 4.6 Spring Boot 整合 Redis

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 4.7 Spring Boot 整合Swagger2搭建Restful API 在线文档

```
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency> 
```

### 4.8 Spring Boot 整合kafka

```
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>2.2.2.RELEASE</version>
</dependency>

<!-- spring-cloud-stream-binder-kafka -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-stream-binder-kafka</artifactId>
</dependency>
```

### 4.9 Spring Boot 整合MongoDB

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

### 4.10 Spring Validator 校验机制

AOP切面拦截@Validator实现

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

### 4.11 加密SpringBoot的敏感配置信息：jasypt-spring-boot

```
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>1.14</version>
</dependency>
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot</artifactId>
    <version>1.14</version>
</dependency>
```

## 第5章 服务注册发现Eureka

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka-server</artifactId>
</dependency>
```

## 第6章 负载均衡Ribbon

## 第7章 声明式调用Feign

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```

## 第8章 熔断器Hystix

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```

## 第9章 路由网关Spring Cloud Zuul

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zuul</artifactId>
</dependency>
```

## 第10章 配置中心Spring Cloud Config

## 第11章 服务链路追踪Spring Cloud Sleuth

## 第12章 微服务监控Spring Cloud Admin

Spring Boot Admin 用于监控基于 Spring Boot 的应用，它是在 Spring Boot Actuator 的基础上提供简洁的可视化 WEB UI。

Spring Boot Admin 提供了很多功能，如显示 name、id 和 version，显示在线状态，Loggers 的日志级别管理，Threads 线程管理，Environment
管理等

## 第13章 Spring Boot Security详解

```
dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## 第14章 使用Spring Cloud OAuth2保护微服务系统

## 第15章 使用 Spring Security OAuth2 JWT 保护微服务系统

## 第16章 使用 Spring Cloud 构建微服务综合案例














