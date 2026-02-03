---
layout: post
title: 模板引擎
category: web
tags: [ web ]
---

模板引擎

## 参考资料

## 一、FreeMarker

[在线手册](http://freemarker.foofun.cn/)

FreeMarker 是一款 模板引擎： 即一种基于模板和要改变的数据， 并用来生成输出文本(HTML网页，电子邮件，配置文件，源代码等)的通用工具。
它不是面向最终用户的，而是一个Java类库，是一款程序员可以嵌入他们所开发产品的组件。

模板编写为FreeMarker Template Language (FTL)。它是简单的，专用的语言， 不是 像PHP那样成熟的编程语言。
那就意味着要准备数据在真实编程语言中来显示，比如数据库查询和业务运算， 之后模板显示已经准备好的数据。
在模板中，你可以专注于如何展现数据， 而在模板之外可以专注于要展示什么数据。

## 二、Thymeleaf

[Thymeleaf入门到吃灰](https://www.cnblogs.com/msi-chen/p/10974009.html)    
[Thymeleaf 教程](https://fanlychie.github.io/post/thymeleaf.html)

thymeleaf是用来开发Web和独立环境项目的服务器端的Java模版引擎  
Spring官方支持的服务的渲染模板中，并不包含jsp。而是Thymeleaf和Freemarker等，而Thymeleaf与SpringMVC的视图技术，及SpringBoot的自动化配置集成非常完美，几乎没有任何成本，你只用关注Thymeleaf的语法即可。

**Thymeleaf的特点**

- 动静结合：
  Thymeleaf 在有网络和无网络的环境下皆可运行，即它可以让美工在浏览器查看页面的静态效果，也可以让程序员在服务器查看带数据的动态页面效果。这是由于它支持
  html 原型，然后在 html 标签里增加额外的属性来达到模板+数据的展示方式。浏览器解释 html 时会忽略未定义的标签属性，所以
  thymeleaf 的模板可以静态地运行；当有数据返回到页面时，Thymeleaf 标签会动态地替换掉静态内容，使页面动态显示。

- 开箱即用：
  它提供标准和spring标准两种方言，可以直接套用模板实现JSTL、 OGNL表达式效果，避免每天套模板、该jstl、改标签的困扰。同时开发人员也可以扩展和创建自定义的方言。

- 多方言支持：
  Thymeleaf 提供spring标准方言和一个与 SpringMVC 完美集成的可选模块，可以快速的实现表单绑定、属性编辑器、国际化等功能。

- 与SpringBoot完美整合
  SpringBoot提供了Thymeleaf的默认配置，并且为Thymeleaf设置了视图解析器，我们可以像以前操作jsp一样来操作Thymeleaf。代码几乎没有任何区别，就是在模板语法上有区别。

## 三、Velocity介绍

[Velocity教程](https://www.jianshu.com/p/5913903324ff)

Velocity是一个基于Java的模板引擎，通过特定的语法，Velocity可以获取在java语言中定义的对象，从而实现界面和java代码的真正分离，这意味着可以使用velocity替代jsp的开发模式了(
实际上笔者所在的公司已经这么做了)。
这使得前端开发人员可以和 Java 程序开发人员同步开发一个遵循 MVC 架构的 web 站点，在实际应用中，velocity还可以应用于很多其他的场景.

Velocity是一个基于Java的模板引擎，其提供了一个Context容器，在java代码里面我们可以往容器中存值，然后在vm文件中使用特定的语法获取，这是velocity基本的用法，其与jsp、freemarker并称为三大视图展现技术，相对于jsp而言，velocity对前后端的分离更加彻底：在vm文件中不允许出现java代码，而jsp文件中却可以.

作为一个模块引擎，除了作为前后端分离的MVC展现层，Velocity还有一些其他用途，比如源代码生成、自动email和转换xml等






