---
layout: post
title: 以领域对象为核心的架构模型
category: development
---

领域架构模型
----------------------------------
前两年，针对[领域模型（Domain Model）](http://martinfowler.com/eaaCatalog/domainModel.html)有过一场声势浩大的讨论，JavaEye的论坛上整天都是关于Rich Domain Model和Anemic Domain Model的讨论，Jdon上也开始鼓吹[DDD](http://en.wikipedia.org/wiki/Domain-driven_design)。影响比较大的两本书是Martin Fowler那本大名鼎鼎的[《企业应用架构模式》(PoEAA)](http://book.douban.com/subject/4826290/)和 Eric Evans 的[《领域驱动设计》](http://book.douban.com/subject/1629512/)。此时，恰逢RoR的流行，大家看到一种新颖的Rich Domain Model的实现方式，而此前Java这样的社区普遍采用的是[Transaction Script](http://martinfowler.com/eaaCatalog/transactionScript.html)这种方式。受RoR的启发，Java社区也涌现出很多框架试图以RoR的方式来编写领域对象，结果由于Java语言的静态特性，这些尝试并不成功，其中比较失败的包括为了实现Rich Domain不惜使用AsprctJ在编译时来生成一坨坨莫名其妙的辅助代码的Spring Roo项目。当然反对的声音也不少，比如Bob大叔，他就认为RoR这种方式违反了“单一职责”和“依赖倒置”等原则。

论战最后趋于冷静，但领域模型的价值被大家广泛认可了。下面是一张描述领域模型的架构图：

![领域模型架构图](/assets/img/domain-architecture.jpg)


Java中的实现
--------------------------------------
诚如Bob大叔所言，以及DDD所倡导的那样，实现Rich Domain不一定要像RoR那样把数据库操作都放在Model对象里。吸取了教训的Spring团队后来做出的Spring Data项目就很不错。那像Java这样的语言实现的应用如何对应上面的架构图呢？我们以AppFuse的结构来做说明：

- 核心的Domain Model并不是JPA的Entity，应该是Entity+Service。
- Repository(Dao)提供了internal persistence port的功能
- application layer就是Spring的Controller。它提供HTML应用，也可以暴露REST API。

这里需要说明的一点是，Entity中不能直接访问Dao（一是由于静态语言的限制，二是由于Bob说的面向对象原则的约束），但并不意味着业务逻辑要写在Service里，这样就回退到了Transaction Script模式了。恰恰相反，要把业务逻辑尽可能抽象成与Repository无关的形式，放在Entity中。Service应该是很薄的一层，负责事务边界等功能。

常见的包结构大概是这个样子：


    - module1
        - web
            - controller
        - api
            - controller
            - vo
        - service
        - facade
        - repository
          
    - module2
    


模块(子系统)管理
-----------------------------
这一节讨论的并不属于领域模型架构的内容。
为了实现模块间的隔离，常需要实现SOA这样的架构，一些实现，比如osgi或SCA就是解决这个问题的。但相对复杂，所以我们这里引入了facade，模块间的调用只能通过facade来实现。我们可以通过编译检查或maven等工具描述模块间的依赖（一个模块分为api和impl两个jar，其他模块只在编译时依赖api这个jar，运行时才依赖impl这个jar）。

另外，实现REST API时也不直接使用Entity对象，而是用一些VO对象来描述数据结构，一个原因是服务于远程服务的VO的粒度往往比Entity更大些，提供更好的远程调用性能的同时也保护了领域模型不对外暴露，另一个原因是，开发客户端用的SDK时，这些VO对象也作为API的一部分可以重用。



