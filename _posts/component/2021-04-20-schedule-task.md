---
layout: post
title: 定时任务
category: component
tags: [component]
---

定时任务总结

## 参考资料

## 一、定时任务简介
### [1.1 JAVA实现定时任务的几种方式](https://blog.csdn.net/kegumingxin2626/article/details/72854823)
- JDK 自带的定时器实现： java.util.Timer类，允许调度一个java.util.TimerTask任务。这种方式可以让程序按照某一个频度执行，但不能在指定时间运行。一般用的较少
- Quartz 定时器实现： 是一个功能比较强大的的调度器，可以让你的程序在指定时间执行，也可以按照某一个频度执行，配置起来稍显复杂
- Spring task任务调度： 可以将它看成一个轻量级的Quartz，而且使用起来比Quartz简单许多

### [1.2 从作业类的继承方式来讲，可以分为两类：](https://blog.csdn.net/Jesse_Suo/article/details/54928773)
- 1.作业类需要继承自特定的作业类基类
    - Quartz中需要继承自org.springframework.scheduling.quartz.QuartzJobBean；
    - java.util.Timer中需要继承自java.util.TimerTask。
- 2.作业类即普通的java类，不需要继承自任何基类。

注:个人推荐使用第二种方式，因为这样所以的类都是普通类，不需要事先区别对待。

### 1.3 从任务调度的触发时机来分，这里主要针对作业使用的触发器，主要有以下两种：
- 1.每隔指定时间则触发一次，在Quartz中对应的触发器为：org.springframework.scheduling.quartz.SimpleTriggerBean
- 2.每到指定时间则触发一次，在Quartz中对应的调度器为：org.springframework.scheduling.quartz.CronTriggerBean

注：并非每种任务都可以使用这两种触发器，如java.util.TimerTask任务就只能使用第一种。Quartz和spring task都可以支持这两种触发条件。

## 二、JDK自带的Timer实现

```
// 1.启动计时器
public class Main {
    public static void main(String[] args) {
        Timer timer = new Timer();
        // 0 ：延时多久执行  300 每隔多久执行一次
        timer.schedule(new WorkTimeTask(), 0, 300);
        /*
         * schedule 和 scheduleAtFixedRate 区别：
         *  可将schedule理解为scheduleAtFixedDelay，
         *  两者主要区别在于delay和rate
         *  1、schedule，如果第一次执行被延时（delay），
         *      随后的任务执行时间将以上一次任务实际执行完成的时间为准
         *  2、scheduleAtFixedRate，如果第一次执行被延时（delay），
         *      随后的任务执行时间将以上一次任务开始执行的时间为准（需考虑同步）
         *  参数：1、任务体    2、延时时间（可以指定执行日期）3、任务执行间隔时间
         */
        timer.scheduleAtFixedRate(task, 0, 1000 * 3);
    }
}

//2.计时器任务管理类
public class WorkTimeTask extends TimerTask {
    @Override
    public void run() {
        System.out.println("Hello World");
    }
}
```

## 三、[Quartz：简单却强大的JAVA作业调度框架](https://blog.csdn.net/Jesse_Suo/article/details/54928773)

### 3.1 Spring整合

```
在spring-context.xml中添加：

<!-- 注册定时执行任务实体 -->
<bean id="xxHandler" class="com.wds.service.xxHandler" init-method="init"></bean>

<!-- 注册定时器信息 -->
<bean id="JKExecuteTriggerJob"
    class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
    <!-- 指定要执行的定时任务类 -->
    <property name="targetObject" ref="xxHandler" />
    <!-- 指定定时器任务类要执行的方法名称 -->
    <property name="targetMethod" value="start" />
    <property name="concurrent" value="false" />
</bean>

<!-- 配置定时器触发规则 -->
<bean id="JKExecuteTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
    <!--声明要运行的实体  -->
    <property name="jobDetail" ref="JKExecuteTriggerJob" />
    <!-- 设置运行时间：每天凌晨2点执行:0 0 2 * * ? -->
    <property name="cronExpression" value="0 0 2 * * ? *"/>
</bean>

<!-- 注册调度器 -->
<bean id="registerQuartz" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
    <!-- 注册定时器实体 集合 -->
    <property name="triggers">
        <list>
            <ref bean="JKExecuteTrigger" />
            <ref bean="reciveGongAnExecuteTrigger" />
        </list>
    </property>
</bean>

```

### 3.2. [Spring Boot 整合 Quartz 实现 Java 定时任务的动态配置](https://mp.weixin.qq.com/s?__biz=MzA3MTUzOTcxOQ==&mid=2452975136&idx=2&sn=10674e5bd21cb0e86d260c334bb40656&chksm=88edc348bf9a4a5e6bb2f246b3f79a413373b54cea594406d60420345e8429d5f837188a92b5&scene=126&sessionid=1600839119&key=ee549ff23b50c79bb30f1019ef7772a2a53d67bb578f16b65767d7f9cde7d2c88f6d6ecbe856a2b3c2a2a0c4c687380f728fe1ac1693f1823bfeac44af1c5aa72233dd0c756c71bbe14ce1eea66dd29455eb41800e732fd9587bcb3c2e31810ca251efa95e7af063d5a10dbb05e381d57fce565a91c3727fa662390b296dbc7d&ascene=1&uin=MTYyMjEyNjk4MQ%3D%3D&devicetype=Windows+10+x64&version=62090529&lang=zh_CN&exportkey=AVVWVkvQ5GN8IQ0gFnr%2BcZI%3D&pass_ticket=1ZRHcMrk8kR5QTAeWwNrdWx%2Fcs3MPn147JxyMRKhwQFyB8mY0HQDloj6S35f53WM&wx_header=0)

## 四、spring-task定时任务工具
Spring3.0以后自主开发的定时任务工具，spring task，可以将它比作一个轻量级的 Quartz，而且使用起来很简单，除spring相关的包外不需要额外的包，而且支持注解和配置 文件两种形式 

### 4.1.配置文件形式
```
<?xml version="1.0" encoding="UTF-8"?> 
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:task="http://www.springframework.org/schema/task"
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.0.xsd" default-lazy-init="false"> 
<!--<bean id="springTaskDemo" class="com.test.SpringTask.SpringTaskDemo" />- ->
<!--直接配置任务bean--> 
<context:component-scan base-package="com.test.SpringTask" />
<!--或者-扫包任 务bean--> 
<task:scheduler id="taskScheduler" pool-size="100" />
<!--调度线程池的大小--> 
<task:scheduled-tasks scheduler="taskScheduler">
<task:scheduled ref="springTaskDemo" method="job1" cron="0/10 * * * * ?"/> 
<task:scheduled ref="springTaskDemo" method="job2" cron="0 0/1 * * * ?"/>
</task:scheduled-tasks> 
</beans> 
```
任务类： 
```
public class SpringTaskDemo { 
    public void job1() { 
    System.out.println("-------我每隔 10 秒 执行一次------------");
    }
    public void job2() { 
    System.out.println("-------我每隔 1 分钟 执行一次------------"); 
    } 
}
```

### 4.2.注解形式
spring定时任务详解（@Scheduled注解） - 在springMVC里使用spring的定时任务非常的简 单，如下： 
>（一）在xml里加入task的命名空间 xmlns:task="http://www.springframework.org/schema/task" http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.1.xs

>（二）启用注解驱动的定时任务   
<task:annotation-driven scheduler="myScheduler"/> 

>（三）配置定时任务的线程池 推荐配置线程池，若不配置多任务下会有问题。后面会详细说明单线程的问题。   
<task:scheduler id="myScheduler" pool-size="5"/> 

>（四）写我们的定时任务： @Scheduled注解为定时任务，cron表达式里写执行的时机 
```
@Component 
public class ATask implements IATask{ 
    @Scheduled(cron="0/10 * * * * ? ") //每10秒执行一次 @Override
    public void aTask(){ 
        try {
            TimeUnit.SECONDS.sleep(20); 
        } catch (InterruptedException e) {               
            e.printStackTrace(); 
        }
        DateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println(sdf.format(DateTime.now().toDate())+"*********A任 务每10秒执行一次进入测试"); 
    }
}
```

### 4.3 springboot定时任务@EnableScheduling
```
//1.通过 @EnableScheduling 注解，开启定时任务调度功能
@EnableScheduling
public class XxxApplication {
	public static void main(String[] args) {
		SpringApplication.run(XxxApplication.class, args);
	}
}

//2.通过 @Scheduled 注解在需要执行的方法上，使用 cronExpression 表达式定义定时任务的执行策略
@Component
public class TaskConfig {
    @Scheduled(cron="0/1 * * * * *")
    public void doJob() {
        // doSomething
    }

}
```

### 4.4.分布式节点-spring-task定时任务执行控制
基于redis锁实现，防止多节点定时任务重复执行

## 五、定时任务-xxljob 
[XXL-JOB的使用(详细教程)](https://blog.csdn.net/f2315895270/article/details/104714692/)

## 六、定时任务框架[quartz、elastic-job和xxl-job的分析对比](https://blog.csdn.net/LWS826528071/article/details/94394249)
![](../../assets/images/2021/job/comp.png)

