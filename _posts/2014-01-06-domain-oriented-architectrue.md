---
layout: post
title: 以领域对象为核心的架构模型
category: development
---

领域架构模型
----------------------------------
前两年，针对[领域模型（Domain Model）](http://martinfowler.com/eaaCatalog/domainModel.html)有过一场声势浩大的讨论，JavaEye的论坛上整体都是关于Rich Domain Model和Anemic Domain Model的讨论，Jdon上也开始鼓吹[DDD](http://en.wikipedia.org/wiki/Domain-driven_design)。影响比较大的两本书是Martin Fowler那本大名鼎鼎的[《企业应用架构模式》(PoEAA)](http://book.douban.com/subject/4826290/)和 Eric Evans 的[《领域驱动设计》](http://book.douban.com/subject/1629512/)。此时，恰逢RoR的流行，大家看到一种新颖的Rich Domain Model的实现方式，而此前像Java社区采用的大部分是[Transaction Script](http://martinfowler.com/eaaCatalog/transactionScript.html)这种方式。Java社区也涌现出很多框架也试图像RoR那样来编写领域对象，结构由于Java语言的静态特性，这些尝试并不成功，其中比较失败的包括像Spring Roo这样昙花一现的项目。当然反对的声音也不少，比如Bob大叔，他就认为RoR这种方式违反了“单一职责”和“依赖倒置”等原则。

论战最后趋于冷静，但领域模型的价值被大家广泛认可了。下面是一张描述领域模型的架构图：

![领域模型架构图](/assets/img/domain-architecture.jpg)