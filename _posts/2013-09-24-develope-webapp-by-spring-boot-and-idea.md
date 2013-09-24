---
layout: post
title: 使用Intellij Idea和Spring Boot开发Web应用
category: development
---

Spring Boot简绍
----------------------------------
Spring Boot是Spring旗下一个新的项目，它本身不提供什么特定的功能，而是提供了一种对现有Spring的各个子项目快速整合的方式。Spring下的多个子项目提供了从Web到数据访问等各个领域的强大易用功能，并可以使用灵活强大的IoC容器对这些子项目进行自由组合。但正因如此，使用Spring搭建的项目其结构也是千姿百态，一直以来，都缺乏像RoR或Django这样的全栈式框架提供的的基于约定的组织方式。Grails算一个，但是是基于Groovy的，而基于Java的通过AOP动态生成代码增强的Spring Roo承载了太多的目标，雄心勃勃但并不成功，此次的Spring Boot的目标要比Spring Roo的精简且专注的多，并特别强调了只是提供整合，不会生成任何代码。

其主要目标是：


- To provide a radically faster and widely accessible 'getting started' experience for all Spring development


- To be opinionated out of the box, but get out of the way quickly as requirements start to diverge from the defaults


- To provide a range of non-functional features that are common to large classes of projects (e.g. embedded servers, security, metrics, health checks, externalized configuration)

>Spring Boot does not generate code and there is absolutely no requirement for XML configuration.

和其它子项目的关系：

![Spring Boot架构图](https://github-camo.global.ssl.fastly.net/6b131abe687510e0b7d69991f57cfe05fb08fdd0/687474703a2f2f626c6f672e737072696e67736f757263652e6f72672f77702d636f6e74656e742f75706c6f6164732f323031332f30382f737072696e672e706e67)

详细的介绍见 [Spring Boot – Simplifying Spring for Everyone](http://spring.io/blog/2013/08/06/spring-boot-simplifying-spring-for-everyone) ,关注[spring.io]和[spring-projects/spring-boot](https://github.com/spring-projects/spring-boot)上的内容。

示例开发
---------------------

现在[spring.io]上guides里的示例大多都是用Spring Boot开发的。开发一个Web应用可以看[这篇文章](http://spring.io/guides/gs/serving-web-content/)。这里的目的主要是把里面最终的示例在IntelliJ Idea里跑起来，并让其支持调试和代码热加载功能。

### 下载示例代码 ###

	git clone https://github.com/spring-guides/gs-serving-web-content.git


### 导入工程到IntelliJ Idea ###

在IntelliJ Idea中选择导入工程，选择complete目录下的build.gradle即可。Idea相当的智能，它认识gradle项目，会自动下载依赖并构建。

### 运行并让对修改热加载 ###

直接在Idea中debug工程中的Application.java就可以了，这样自然是支持调试的。在浏览器里访问：

	http://localhost:8080/greeting?name=yanhua365

就能看到运行结果了。

如果修改了Java代码，需要执行“Build > Make Project”命令（可以使用Ctrl+F9快捷键），然后Idea提示是否要reload修改后的class，选择确认即可。

如果要支持修改了Thymeleaf模板而不需要重启程序，需要在```src/main/resources/```下建立一个```application.properties```文件，加入如下内容：

	spring.thymeleaf.cache: false

每次修改了模板后，同样需要Make一下。刷新浏览器，马上就可以看到修改生效了。

Spring Boot更多的功能还在进一步探索中，大家一起来关注这个称手的新项目吧。


[spring.io]: http://spring.io/