## 个人博客

这是我的个人博客项目，里面会记录技术学习的点滴。 访问地址：[https://wdsheng0i.github.io/](https://wdsheng0i.github.io/)  

## 规划
## 一、data-algorithm 数据结构 & 算法总结
### 1、数据结构
- 1.数组(Arrays)：
    - 1.1.有序数组：
    - 1.2.静态数组：
    - 1.3.动态数组(Vector)：
- 2.线性表(ArrayList)：
    - 2.1.顺序表：
    - 2.2.链表(物理结构指针)(LinkedList)：
    - 2.3.单链表：
    - 2.4.双链表：
    - 2.4.循环链表：
    - 2.5 静态链表：借助数组实现
- 4.哈希表Hash：
- 5.栈(Stack)：也称为堆栈，是一种线性表
- 6.堆：是一种经过排序的树形数据结构(二叉树)
- 7.队列(Queue)： 先进先出FIFO，队列也是一种特殊的线性表。
- 8.树
    - 8.1 二叉树：
    - 8.2 满二叉树：
    - 8.3 完全二叉树：
    - 8.4 二叉查找树（Binary Search Tree）：
    - 8.5 平衡二叉查找树（AVL）：
    - 8.6 红黑树(自平衡二叉查找树))
    - 8.7 B树（Balanced-tree）：
    - 8.8 B+树：加强版的多路平衡查找树
    - 8.9 字典树：
    - 8.10 排序树：
    - 8.11 R树：
    - 8.12 多路(叉)树：一个节点有多个子节点
    - 8.13 二叉树遍历
- 9.图

### 2、算法
- 2.0 算法复杂度
    - 时间复杂度
    - 空间复杂度
- 2.1 搜索算法
    - 顺序查找
    - 二分查找
    - 哈希(HASH)查找
    - 深度优先遍历查找(图)
- 2.2 排序算法
    - 插入排序(直接插入排序、希尔排序)
    - 选择排序(直接选择排序、堆排序)
    - 交换排序(冒泡排序、快速排序)
    - 归并排序
- 2.3 递归算法
- 2.4 递推算法
- 2.5 暴力求解算法(穷举法)
- 2.6 分治算法
- 2.7 动态规划算法
- 2.8 贪心算法
- 2.9 分支界定
- 2.10 回溯法：
- 2.11 图论模型与算法

## 二、design-patterns 设计原则 & 设计模式总结
### 1、[设计原则](http://www.cnblogs.com/lanxuezaipiao/archive/2013/06/09/3128665.html  )
- 总原则： 开闭原则（Open Close Principle）
- 1、单一职责原则：Single Responsibility Principle(简称SRP)
- 2、里氏代换原则（Liskov Substitution Principle）
- 3、依赖倒转原则（Dependence Inversion Principle）
- 4、接口隔离原则（Interface Segregation Principle）
- 5、迪米特法则（最少知道原则）（Demeter Principle）
- 6、 合成复用原则（Composite Reuse Principle）
- 其他原则

### 2、[设计模式23种](https://www.iteye.com/blog/zz563143188-1847029)
#### 创建型模式，共五种：
- 工厂方法模式
- 抽象工厂模式
- 单例模式
- 建造者模式
- 原型模式。

#### 结构型模式，共七种：
- 适配器模式
- 装饰器模式
- 代理模式
- 外观模式
- 桥接模式
- 组合模式
- 享元模式

#### 行为型模式，共十一种：
- 策略模式
- 模板方法模式
- 观察者模式
- 迭代子模式
- 责任链模式
- 命令模式
- 备忘录模式
- 状态模式
- 访问者模式
- 中介者模式
- 解释器模式

#### 并发型模式和线程池模式

## 三、common-utils 基础编码能力总结
编码总结与代码复用，部分待总结梳理  
```
学习参考博客：
纯洁的微笑博客系列：http://www.ityouknow.com/spring-boot.html
死牛胖子的技术随笔：https://blog.csdn.net/gongm24/category_6742927.html?spm=1001.2014.3001.5482
```
- 1.日志切面(接口日志、操作日志): LogAspect
- 2.防重复提交切面: RepeatSubmitAspect
- 3.请求重试切面: RetryAspect
- 4.[数据库配置加解密: propertycrypt](https://blog.csdn.net/sinat_40294534/article/details/78019858)
- 5.[DB业务字段存储脱敏处理fieldcrypt：数据出入库字段mybatis拦截器加解密](https://www.jianshu.com/p/1673344f9b58)
- 6.各种常用加解密算法security: SHA、RSA、AES、DES、SM4、MD5、Base64、Hex、签名
- 7.自定义业务异常统一处理exception: ServiceException + ServiceExceptionHandler
- 8.使用配置属性properties: 
- 9.Ehcache缓存集成使用: ehcache
- 10.[javax.validation参数校验示例：](https://mp.weixin.qq.com/s/2wHEyemPR_POruvTGBmQmA)
    - [Validated数据校验，看这一篇就够了](https://blog.csdn.net/weixin_43990804/article/details/112974137)
- 11.验证码生成: validcode
- 12.RestTemplate发送HTTP请求: 配置、使用
- 13.常用文件转化: fileconvert/(img、pdf、tif、word、html)
- 14.拦截器HandlerInterceptor
    - token验证
    - session校验
- 15.WebMvcConfigurer配置拦截器及过滤静态资源: 
- 16.WebMvcConfigurer使用CROS解决跨域问题: 
- 17.配置过滤器Filter
- 18.配置监听器HttpSessionListener
- 19.封装统一接口返回ResultBean、分页返回PageResult: 
- 20.API接口鉴权：apioauth
    - 内部应用：拦截器 + 注解实现（超管接口、企管接口、需要**权限字接口）
    - 外部应用：appId + appSecret + timeStamp + signture实现
    - 提供mastkey或者固定token形式：由账号经过固定算法生成，与账号一一对应
- 21.各种常用工具类汇总、建议比较使用hutool

## 四、spring-boot-ability 工程能力总结
- 0 [spring-boot-devtools实现springboot热部署](https://blog.csdn.net/qq_27384769/article/details/79473992)
- 1.yml配置
- 2.springBoot集成日志组件：slf4j + logback + sql日志记录
- 3.springBoot集成Lombok 
- 4.springBoot集成Druid 
- 5.springBoot集成Mybatis 
- 6.springBoot集成**Mybatis-Generator**进行代码自动生成
- 7.springBoot集成Tk-mybatis、或者mybatis-plus、JAP
    - Tk-mybatis扩展: 自定义provider 
- 8.springBoot集成PageHelper实现分页查询
- 9.springBoot集成Shiro实现登录、鉴权、rememberMe
    - [spring-session集成](https://mp.weixin.qq.com/s/A9CJoa55G_NPPIp0IRFJBw)
- 10.spring-scheduleTask实现定时任务
- 11.springBoot集成Kafka 
- 12.springBoot集成Redis
    - redis缓存使用
    - spring-cache 使用Redis
    - Redis失效key监听应用
    - [Redis延时队列的简单实现](https://mp.weixin.qq.com/s/NeLCIhf65GCdxeHC4rDewA)
- 13.springBoot集成Mongodb 
- 14.springBoot集成Junit单元测试
- 15.springBoot集成Thymeleaf模板引擎 
- 16.springboot线程池配置使用ThreadPoolTaskExecutor
- 17.springboot异步任务@Async注解
- 18.springboot文件上传upload  
    - 上传文件大小限制
- 19.springboot文件下载
- 20 springboot整合swagger2
- 21.springboot自定义项目Favicon
- 22.springboot定时任务@EnableScheduling
- 23.spring-retry[重试框架集成使用](https://mp.weixin.qq.com/s/tMza3K_qB0OpV36Z-4OqXA)
- 24.springboot[集成websocket](https://mp.weixin.qq.com/s/3Y4EVaeTUnZed169QZhqgg)
- 25.springboot[自定义starter](https://mp.weixin.qq.com/s/4XgsuxvqGTCbPPXU72QPzg)  
- [Spring Boot 启动时，让方法自动执行的 4 种方法](https://mp.weixin.qq.com/s/QLC2YY00-p28pTkOKiK2Gw)
---  
- springBoot集成[任务调度框架Quartz](https://mp.weixin.qq.com/s/iJjAMnt6oPEx2x-jhh-2zA)  
- springboot邮件发送mail 
- springboot整合ElasticJob搜索  
- springBoot整合activiti实现工作流  
- springBoot登录鉴权：[SpringSecurity + JWT](https://mp.weixin.qq.com/s/9nRyVD2XkOkQHPr8kuE8UA)
- springboot登录鉴权：oauth2 + JWT  
    - [OAuth 2.0 的四种方式](http://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html)
- springBoot集成Fastdfs存储
- springBoot集成oss存储
- springBoot集成memcache
- Spring Boot整合[Elastricsearch + LogStash + Kibana](https://mp.weixin.qq.com/s/7YKJ_crjfVQZEq2YcIm_GQ)
    - [ELK 处理 Spring Boot 日志](https://mp.weixin.qq.com/s/nOVQAZWKzMhGJDOayVuiPA)

## 五、spring-cloud-ability 微服务能力总结
### 1.服务注册发现组件：eureka

### 2.服务调用组件：feign

### 3.服务网关组件
- zuul
- getway

### 4.服务配置中心组件
- ctrip-apollo
- spring-cloud-config

### 5.服务监控组件
- spring-boot-admin
- spring-boot-actuator
- spring-cloud-admin

### 6.服务容错组件：hystrix

### 7.服务负责均衡组件：
- Ribbon
- Nginx

### 8.服务链路跟踪组件：spring-cloud-sleuth

### 9.服务消息总线：BUS 

## 六、spring-cloud-alibaba-ability 微服务能力总结
### spring cloud alibaba：Sentinel流量控制、熔断降级

### spring cloud alibaba：nacos配置中心、服务注册、发现

## 七、bigdata-ability大数据能力总结

## 八、高并发、高可用方案设计总结
- 1.高可用部署方案：LVS + Keepalived + Nginx实现 **动静分离、反向代理、集群、负载均衡、主从热备、双机主备** 
- 2.高可用redis缓存方案：主从复制、Redis集群、哨兵监控
- 3.高可用DB数据库方案：Mycat配置实现-mysql集群、主从复制、读写分离、负载均衡、分库分表
- 4.高并发下服务降级、限流方案
- 5.CND加速静态文件访问
- 6.应用容灾及机房规划
- 7.系统扩容机制

