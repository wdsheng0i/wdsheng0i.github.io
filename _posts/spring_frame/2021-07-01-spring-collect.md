---
layout: post
title: Spring SpringMVC 学习资料汇总
category: springcloud
tags: [springcloud]
---

## 参考资料 

[死牛胖子的技术随笔](https://blog.csdn.net/gongm24/category_6742927_2.html)：https://blog.csdn.net/gongm24/category_6742927_2.html

## 一、Spring framework体系结构
![](https://wdsheng0i.github.io/assets/images/2021/spring/springfrm.png) 
  
- 1.spring-core：依赖注入IoC与DI的最基本实现
- 2.spring-beans：Bean工厂与bean的装配
- 3.spring-context：spring的context上下文即IoC容器
- 4.spring-expression：spring表达式语言
- 5.spring-jdbc：jdbc的支持
- 6.spring-tx：事务控制
- 7.spring-orm：对象关系映射，集成orm框架
- 8.spring-oxm：对象xml映射
- 9.spring-jms：java消息服务
- 10.spring-web：基础web功能，如文件上传  
- 11.spring-webmvc：mvc实现  
- 12.spring-webmvc-portlet：基于portlet的mvc实现  
- 13.spring-websocket：为web应用提供的高效通信工具  
- 14.spring-aop：面向切面编程  
- 15.spring-aspects：集成AspectJ 
- 16.spring-instrument：提供一些类级的工具支持和ClassLoader级的实现，用于服务器  
- 17.spring-instrument-tomcat：针对tomcat的instrument实现  
- 18.spring-messaging：用于构建基于消息的应用程序
- 19.spring-test：spring测试，提供junit与mock测试功能  
- 20.spring-context-support：spring额外支持包，比如邮件服务、视图解析等 

## 二、Spring-AOP 
AOP利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的行为封装到一个可重用模块，并将其命名为“Aspect”，即方面。所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任，例如事务处理、日志管理、权限控制等，封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。进而改变这些行为的时候不影响业务逻辑的代码。
 
当我们需要为分散的对象引入公共行为的时候，OOP则显得无能为力。也就是说，OOP允许你定义从上到下的关系，但并不适合定义从左到右的关系。例如日志功能。日志代码往往水平地散布在所有对象层次中，而与它所散布到的对象的核心功能毫无关系。对于其他类型的代码，如安全性、异常处理和透明的持续性也是如此。这种散布在各处的无关的代码被称为横切（cross-cutting）代码，在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。   
 
AOP（Aspect Oriented Programming），即面向切面编程，可以说是OOP（Object Oriented Programming，面向对象编程）的补充和完善。OOP引入封装、继承、多态等概念来建立一种对象层次结构，用于模拟公共行为的一个集合。不过OOP允许开发者定义纵向的关系，但并不适合定义横向的关系，例如日志功能。日志代码往往横向地散布在所有对象层次中，而与它对应的对象的核心功能毫无关系对于其他类型的代码，如安全性、异常处理和透明的持续性也都是如此，这种散布在各处的无关的代码被称为横切（cross cutting），在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。
 
AOP技术恰恰相反，它利用一种称为"横切"的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其命名为"Aspect"，即切面。所谓"切面"，简单说就是那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的耦合度，并有利于未来的可操作性和可维护性。
 
使用"横切"技术，AOP把软件系统分为两个部分：核心关注点和横切关注点。业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处基本相似，比如权限认证、日志、事物。AOP的作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开来。


### 2.1.适合切面处理场景：
- 事务处理、
- 日志管理、
- 异常处理、
- 单值代码翻译、组织机构翻译
- 注解实现字段加解密-出入库
- 记录程序（类、方法）运行时间 --性能统计
- 权限控制、权限检查

### 2.2.spring-aop核心概念
Spring中AOP代理由Spring的IOC容器负责生成、管理，其依赖关系也由IOC容器负责管理。 
 
**Spring创建代理的原理：**  
[太好了! 总算有人把动态代理、CGlib、AOP都说清楚了](https://s2.uczzd.cn/webview/news?app=uc-iflow&aid=9286449289265789223&cid=1525483516&zzd_from=uc-iflow&uc_param_str=dndsfrvesvntnwpfgicp&recoid=1733089380403571729&rd_type=share&sp_gz=0&pagetype=share&btifl=100&uc_share_depth=1)  
[Spring AOP的实现原理及应用场景（通过动态代理）](https://mp.weixin.qq.com/s/l_UuzWNmAfvKNUDAVii0tA)  
- 1、默认使用Java动态代理来创建AOP代理，这样就可以为任何接口实例创建代理了(基于反射实现，实现接口)
- 2、当需要代理的类不是代理接口的时候，Spring会切换为使用CGLIB代理，也可强制使用CGLIB代理（也叫做子类代理）

**AOP编程** 
- 1、定义普通业务组件
- 2、定义切入点，一个切入点可能横切多个业务组件
- 3、定义增强处理，增强处理就是在AOP框架为普通业务组件织入的处理动作

``` 
1、横切关注点
    对哪些方法进行拦截，拦截后怎么处理，这些关注点称之为横切关注点
2、切面（aspect）
    类是对物体特征的抽象，切面就是对横切关注点的抽象
3、连接点（joinpoint）
    被拦截到的点，因为Spring只支持方法类型的连接点，所以在Spring中连接点指的就是被拦截到的方法，实际上连接点还可以是字段或者构造器
4、切入点（pointcut）
    对连接点进行拦截的定义
5、通知（advice）
    通知指的就是指拦截到连接点之后要执行的代码，分为前置、后置、异常、最终、环绕通知五类
    @Before：前置通知，在方法之前执行。
    @After：后置通知，方法之后执行。
    @AfterReturning：返回通知，方法返回结果后执行。
    @AfterThrowing：异常通知，在方法抛出异常之后。
    @Around：环绕通知，围绕方法执行。
6、目标对象
    代理的目标对象
7、织入（weave）
    将切面应用到目标对象并导致代理对象创建的过程
8、引入（introduction）
    在不修改代码的前提下，引入可以在运行期为类动态地添加一些方法或字段
```

**aop实现日志切面示例**：https://www.cnblogs.com/chihirotan/p/6228337.html

### 2.3 切面失效场景
- [解决AOP切面在嵌套方法调用时不生效问题](https://blog.csdn.net/xiaotian5180/article/details/105518564)
- [Cannot find current proxy: Set 'exposeProxy' property on Advised to 'true' to 以及Spring事务失效的原因和解决方案](https://blog.csdn.net/mameng1988/article/details/85548812)
- [从@Async案例找到Spring框架的bug：exposeProxy=true不生效原因大剖析+最佳解决方案](https://cloud.tencent.com/developer/article/1497700)

spring声明式事务，实现原理也是切面注解，上诉原因同样适用于事务失效场景

## 三、Spring-IOC
IOC的思想最核心的地方在于，资源不由使用资源的双方管理，而由不使用资源的第三方管理，这可以带来很多好处。第一，资源集中管理，实现资源的可配置和易管理。第二，降低了使用资源双方的依赖程度，也就是我们说的耦合度。 
 
### 3.1 依赖注入(Dependency Injection)和控制反转(Inversion of Control)
 - 所谓的依赖注入，则是，甲方开放接口，在它需要的时候，能够讲乙方传递进来(注入)
 - 所谓的控制反转，甲乙双方不相互依赖，交易活动的进行不依赖于甲乙任何一方，整个活动的进行由第三方负责管理。
 
 这就是spring IOC的思想所在
 
 1.传统的形式：
- controller调用service中的方法，要在controller中new一个service对象，产生依赖关系而且，new出来的对象只能本controller使用，耦合度高，
- service中调用dao层的方法也是，需要依赖new一个dao对象，产生耦合
 
 2.Spring的控制翻转实现方式就是依赖注入：
- 1).所有的Controller,service和dao类 使用 @Repository、@Component、@Service 和 @Constroller 注解
- 2).配置springMvc扫描<context:component-scan base-package="com.xx.*.controller" />
- 3).Spring会自动创建相应的 BeanDefinition 对象，并注册到 ApplicationContext 中这些类就成了 Spring受管组件 , 单例模式
- 4).资源不由使用资源的双方管理控制, 而是交给spring创建和注入，就是控制反转 
- 5).然后就可以在controller中通过 @Autowired使用bean

## 四、springMVC
[跟着开涛学SpringMVC](https://www.iteye.com/blog/jinnianshilongnian-1631021)      
https://pan.baidu.com/s/1-GZsONQxoaFJblv4SvZjog 密码：0yol    

![](https://wdsheng0i.github.io/assets/images/2021/spring/mvc2.png)

### 4.1 springMVC常用注解 
1.Spring2.5引入注解式处理器支持     
需通过处理器映射器DefaultAnnotationHandlerMapping和处理器适配器AnnotationMethodHandlerAdapter来开启支持@Controller 和 @RequestMapping注解的处理器。
  
- @Autowired
- @Controller+@ResponseBody = @RestController
- @Controller：用于标识是处理器类；
- @ResponseBody
- @Service
- @Repository
- @Component
- @RequestMapping：请求到处理器功能方法的映射规则；RequestMapping注解有六个属性, 例: @RequestMapping(value = "/testMethod", method = RequestMethod.POST, consumes="application/json", produces="application/json", params = {"username","age!=10" }, headers = { "Accept-Language=US,zh;q=0.8" })
    - value：指定请求的实际地址，指定的地址可以是URI Template 模式；
    - method： 指定请求的method类型， GET、POST、PUT、DELETE等
    - consumes： 指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;
    - produces: 指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；
    - params： 指定request中必须包含某些参数值时，才让该方法处理。
    - headers： 指定request中必须包含某些指定的header值，才能让该方法处理请求。
- @ModelAttribute：请求参数到命令对象的绑定；
- @SessionAttributes：用于声明session级别存储的属性，放置在处理器类上，通常列出模型属性（如@ModelAttribute ：对应的名称，则这些属性会透明的保存到session中；
- @InitBinder：自定义数据绑定注册支持，用于将请求参数转换到命令对象属性的对应类型；
- @ConfigurationProperties
- @configuration
- @CrossOrigin 

2.Spring3.0引入RESTful架构风格支持 
- @PathVariable：请求URI中的模板变量部分到处理器功能处理方法的方法参数上的绑定，从而支持RESTful架构风格的URI；
- @RequestParam：请求参数到处理器功能处理方法的方法参数上的绑定；
- @CookieValue：cookie数据到处理器功能处理方法的方法参数上的绑定；
- @RequestHeader：请求头（header）数据到处理器功能处理方法的方法参数上的绑定；
- @RequestBody：请求的body体的绑定（通过HttpMessageConverter进行类型转换）；
- @ResponseBody：处理器功能处理方法的返回值作为响应体（通过HttpMessageConverter进行类型转换）；
- @ResponseStatus：定义处理器功能处理方法/异常处理器返回的状态码和原因；
- @ExceptionHandler：注解式声明异常处理器；

3.SpringBoot 引入组合注解
- @PostMapping
- @GetMapping
...  

### 4.2 SpringMVC[九大组件](https://www.cnblogs.com/chinaifae/articles/10275596.html)
SpringMVC中的Servlet一共有三个层次，分别是HttpServletBean、FrameworkServlet和 DispatcherServlet。
- HttpServletBean直接继承自java的HttpServlet，其作用是将Servlet中配置的参数设置到相应的属性；
- FrameworkServlet初始化了WebApplicationContext，
- DispatcherServlet初始化了自身的9个组件。


DispatcherServlet的九个组件
- 【1. HandlerMapping】
	- 是用来查找Handler的。收到请求后用哪个Handler进行处理就是HandlerMapping需要做的事。
- 【2. HandlerAdapter】
	- 一个适配器。SpringMVC中的Handler可以是任意的形式，但是Servlet需要的处理方法的结构却是固定的。让固定的Servlet处理方法调用灵活的Handler来进行处理就是HandlerAdapter要做的事情。
- 【3. HandlerExceptionResolver】
	- 此组件的作用是根据Handler异常设置ModelAndView，之后再交给render方法进行渲染。
- 【4. ViewResolver】
	- ViewResolver用来将String类型的视图名和Locale解析为View类型的视图。用来渲染页面
- 【5. RequestToViewNameTranslator】
	- 从request中获取ViewName。RequestToViewNameTranslator在Spring MVC容器里只可以配置一个，所有request到ViewName的转换规则都要在一个Translator里面全部实现。
- 【6. LocaleResolver】
	- 解析视图需要两个参数：一是视图名，另一个是Locale。视图名是处理器返回的，LocaleResolver用于从request解析出Locale。SpringMVC主要有两个地方用到了Locale：一是ViewResolver视图解析的时候；二是用到国际化资源或者主题的时候。
- 【7. ThemeResolver】
	- 用于解析主题。SpringMVC中一个主题对应一个properties文件
- 【8. MultipartResolver】
	- 用于处理上传请求。处理方法是将普通的request包装成MultipartHttpServletRequest。
- 【9. FlashMapManager】
	- 用来管理FlashMap的，FlashMap主要用在redirect中传递参数。 

### 4.3 controller接口参数获取和返回值方式总结
``` 
##############接口获取参数##############
## POST:
### 1.@RequestBody：RequestBody + 封装业务对象

@RequestMapping(method = POST, path = "/user/add")
public void addUser(@RequestBody User user){
    ...
}


### 2.@ModelAttribute：ModelAttribute + 封装参数Param对象

@RequestMapping(method = POST, path = "/user/add")
public void addUser(@ModelAttribute User user){
    ...
}

### 3.@RequestBody + Json-param
  
@RequestMapping("/user/add")
public void addUser( @RequestBody String userStr) {
    ...
}

### 4.Form表单数据提交submit + 封装业务y对象
 
@PostMapping(value = "/user/add")
public void addUser(User user) {
    ...
}


## GET:
### 1.@PathVariable  

@RequestMapping("/user/{userId} ")  
public void getData( @PathVariable("userId") String userId) {
    ...
}

### 2.@RequestParam

//拼接在路由后的参数获取：http://127.0.0.1:8080/userinfo?name=Tim
@RequestMapping("/list")
public void queryAjpcAjbh(@RequestParam(value="name") String name){
    ...
}


### 3.HttpServletRequest

//拼接在路由后的参数获取：http://127.0.0.1:8080/userinfo?name=Tim
@RequestMapping("/ajxx")
public void queryAjxq(HttpServletRequest request, HttpServletResponse response){
    String name = request.getParameter("name"); ...
} 



############## 向页面返回数据，或者跳转页面  ############## 
### 1.@ResponseBody/@RestController + json数据/业务对象
 
@RequestMapping("/login.do")
@ResponseBody
public Object login(String name, String password ) {
	user = userService.login(name, password); 
	return new JsonResult(user);
}
 
### 2.使用ModelAndView对象
 
@RequestMapping("/login.do")  
public ModelAndView  login(String name,String pass){  
    User user = userService.login(name,pwd);  
    Map<String,Object> data = new HashMap<String,Object>();  
    data.put("user",user);  
    return new ModelAndView("success", data); //success.html页面
}
 
### 3.使用ModelMap参数对象
ModelMap数据会利用HttpServletRequest的Attribute传值到success.jsp中 
 
@RequestMapping("/login.do")  
public　String login(String name,String pass , ModelMap model){  
    User user  = userService.login(name,pwd);  
    model.addAttribute("user",user);  
    model.put("name",name);  
    return "success";  //success.html页面
}   
 
 
### 4.Session存储：
可以利用HttpServletReequest的getSession()方法 
 
@RequestMapping("/login.do")  
public String login(String name, String pwd , HttpServletRequest request){  
     User user = userService.login(name, pwd);  
     HttpSession session = request.getSession();  
     session.setAttribute("user",user);  
     return "success";  //success.html页面
}
 
### 5.Spring MVC  请求转发
默认采用的是转发来定位视图，如果要使用重定向，可以如下操作  
1，使用RedirectView  
2，使用redirect:前缀 
 
public ModelAndView login(){  
   RedirectView view = new RedirectView("regirst.do");  
   return new ModelAndView(view); 
}  
   
// 或者用如下方法，工作中常用的方法： 
public String login(){  
    //TODO  
    return "redirect:regirst.do";  
}
 
```

## 五、spring常见问题
### 5.0 Bean作用域：
scope属性，session、request、page、application...

### 5.1 spring程序是如何启动的？
ContextLoader    
SpringMVC中的Servlet一共有三个层次，分别是HttpServletBean、FrameworkServlet和 DispatcherServlet。  
- HttpServletBean直接继承自java的HttpServlet，其作用是将Servlet中配置的参数设置到相应的属性；
- FrameworkServlet初始化了WebApplicationContext，
- DispatcherServlet初始化了自身的9个组件。  

### 5.2 spring是如何加载配置文件到应用程序的？

### 5.3 bean的初始化都经历了什么

### 5.4 Spring解析，加载及实例化Bean的顺序（零配置）
https://mp.weixin.qq.com/s/MaDYRtR8ROWl63noWCkkJA

### 5.5 Spring Boot条件化Bean 根据配置文件加载对应Bean
https://blog.csdn.net/Applying/article/details/106952021

### 5.6 Spring Bean有没有必要实现Aware接口

### 5.7 彻底理解bean的生命周期
- 创建
- 初始化属性值或者添加引用
- 初始化：init-method
- 使用
- 销毁：destroy-method 

### 5.8 掌握核心接口BeanDefinitionReader

### 5.9 掌握核心接口BeanFactory作用

### 5.10 BeanFactory与ApplicationContext的区别
- ApplicationContext在启动容器时完成所有bean初始化
- BeanFactory 的实现类采用的是 延迟加载，getbean才创建

### 5.11 BeanFactory和FactoryBean的区别

### 5.12 彻底搞懂Spring的refresh方法

### 5.13 BeanPostProcessor接口的作用及实现

### 5.14 BeanFactoryPostProcessor接口的作用及实现

### 5.15 循环依赖问题

### 5.16  ApplicationContext
- ClassPathXMLApplicationContext
- FileSystemXMLApplicationContext
- XmlWebApplicationContext
- AnnotationConfigApplicationContext

### 5.17 IoC容器启动过程
- 读配置、javaconfig容器的启动阶段
- Bean 实例化阶段

### 5.18 aop是如何处理的 
cglib和jdk动态代理的机制  
- JDK动态代理：实现目前对象的相同接口 
- CGLIB动态代理： 继承目标对象、底层asm块
- aspectj-autoproxy proxy-target-class="true"
    - 默认为false，表示使用jdk动态代理织入增强
    - true时，表示使用CGLib动态代理技术织入增强
    - 即使proxy-target-class设置false，如目标类没有声明接口，spring将自动使用CGLib动态代理

### 5.19 Spring 中经典的 9 种设计模式
https://mp.weixin.qq.com/s/kwgV7Rhxv7wV7DOWmS9NzQ
- 1.简单工厂模式
- 2.工厂方法
- 3.单例设计模式
- 4.代理模式
- 5.模板方法
- 6.观察者模式
- 7.适配器模式
- 8.装饰者模式
- 9.策略模式

### 5.20 @Autowired和@Resource的区别
spring2.5提供了基于注解（Annotation-based）的配置，我们可以通过注解的方式来完成注入依赖。在Java代码中可以使用  
@Resource或者@Autowired注解方式来经行注入。虽然@Resource和@Autowired都可以来完成注入依赖，但它们之间是有区 别的。首先来看一下：  
- a.@Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入；
- b.@Autowired默认是按照类型装配注入的，如果想按照名称来转配注入，则需要结合@Qualifier一起使用；
- c.@Resource注解是由JDK提供，而@Autowired是由Spring提供；
- d. @Resource和@Autowired都可以书写标注在字段或者该字段的setter方法之上 

### 5.21 Spring [@Autowired Map 和 List](https://blog.csdn.net/qq_32867467/article/details/82944196)
- 当注入一个Map的时候 ，value泛型为MaoService，则注入后Spring会将实例化后的bean放入value ，key则为注入后bean的名字
- 当注入一个List的时候，List的泛型为MaoService，则注入后Spring会将实例化的bean放入List中

### 5.22 @Component、@Service、@Bean实例化规则 
- @Component、@Service注解的类，实例对象默认beanName是： 类名首字母小写
- @Bean注解的方法，实例对象默认beanName是： 方法名
- 指定实例beanName：@Service("loginService2")、@Service(value = "redisService")、@Bean("restTemplateNotify")、@Bean(name = "restTemplateCustom")

### 5.23 单一实例按类型注入、[多实例按beanName注入或者@Qualifier指定beanName注入](http://t.zoukankan.com/linhongwenBlog-p-13160482.html)
- @Autowired默认是按类型匹配的方式，在容器查找匹配的bean，当且仅有一个匹配的bean时，Spring将其注入到@Autowired所标注的变量中。  
- 如果容器中有一个以上匹配类型的bean时，则可以通过@Qualifier注解限定bean的名称，指定bean名称注入。
```
1.仅存在一个实现类：默认按类型注入
@Service
public class HelloServiceImpl implements HelloService {
    @Override
    public void sayHello() {
        System.out.println("say hello impl");
    }
}

//以下两种写法均可，因为只按HelloService类型找实例
    @Autowired
    private HelloService helloService;
      
    @Autowired
    private HelloService abc;

2.存在多个实现类、默认按beanName注入实例
//再增加一个实现类
@Service
public class NewHelloServiceImpl implements HelloService {
    @Override
    public void sayHello() {
        System.out.println("new say hello impl");
    }
}

    @Autowired
    private HelloService abc; //注入失败，没有beanName = abc的实例对象
    
    @Autowired
    private HelloService helloServiceImpl; //注入成功，HelloServiceImpl的实例对象，HelloServiceImpl类注解@Service默认beanName = helloServiceImpl
    
    @Autowired
    private HelloService newHelloServiceImpl; //注入成功，NewHelloServiceImpl的实例对象，NewHelloServiceImpl类注解@Service默认beanName = newHelloServiceImpl

3.多实例，按beanName注入实例：指定实例beanName，或者指定注入beanName
3.1.注入对象不变，指定实例beanName
@Service("abc")  //修改这个
public class HelloServiceImpl implements HelloService {
    @Override
    public void sayHello() {
        System.out.println("say hello impl");
    }
}

    @Autowired
    private HelloService abc;  //注入成功，HelloServiceImpl类注解@Service指定 beanName = abc

3.2.实例类不变，指定注入beanName
@Service
public class HelloServiceImpl implements HelloService {
    @Override
    public void sayHello() {
        System.out.println("say hello impl");
    }
}
    @Autowired
    @Qualifier("helloServiceImpl")  //增加这个
    private HelloService abc;  //注入成功，@Qualifier("helloServiceImpl")指定注入的 beanName = helloServiceImpl
```

### 5.24 @Primary、 @ConditionalOnMissingBean、@ConditionOnBean
- @primary： 当一个接口有两个或多个实现时，@primary和@Qulifier都可以解决实现类的优先实现问题，区别点在于： @Primary：在实现类上标注，标识该实现类优先选择 @Qulifier：在接口上标注，标识选择哪一个实现类
- @ConditionalOnMissingBean： 应用中不包含某个类时，对应的配置才生效，应用中包含该类的bean，那么二次注册同类型的bean的时候则会报错
- @ConditionOnBean： 与ConditionalOnMissingBean注解相反，检查应用中是否拥有某个bean，若有，则生效。否则报错

### 5.25 0 配置实现