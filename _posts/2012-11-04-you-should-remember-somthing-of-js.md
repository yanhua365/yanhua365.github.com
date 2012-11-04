---
layout: post
title: 理解JavaScript需要记住的最关键的几点
category: development
---

记得三四年前参加过一次CSDN组织的关于编程语言的技术演讲，那时ROR在国内的气势已经热火朝天，各种脚本语言也比以往任何时候更受到开发者的关注，演讲者现场调查了参会者各是那种脚本语言的程序员，200多位参会者中承认自己是Ruby或Python的程序员都不少，而当提到JavaScript时，竟然无人举手。

其实，现场几乎每个人都在或多或少地使用JavaScript，但鲜有人承认自己是JavaScript程序员。

据说当年浏览器大战之时，出于市场战略的考虑，JavaScript的设计者仅用了几天就把它设计出来并用在了网景的浏览器上，快速推向了市场。基于这样的背景，这门语言显得十分诡异，你大可用Quick and Dirty来形容它，它虽然漏洞百出但简单易用，并恰逢其时，就像当年微软QDOS一样，虽然也被形容为Quick and Dirty不受人待见，但却取得了市场上的成功。借着Web的浪潮，JavaScript几乎可以运行在每一台装有浏览器的个人电脑上，它已经成为了世界上使用最广泛的编程语言，这就是Quick带来的好处。但Drity也是如影随形的，它虽然在名字上冠以Java的前缀，但那完全是出于市场考虑，在设计和语法上和Java大相径庭，它易于上手，但其理念与其它的主流编程语言的设计思路完全不同，以至于非常容易让人误解，有人说它是 世界上最被误解的语言，更可怕的是还有很多缺陷和陷阱，幸好有人维护了一份文档，用来讲解 JavaScript 一些古怪用法、如何避免常见的难以发现的错误和问题等。

用好一门编程语言，只熟练其语法是不够的，最重要的是要理解其背后的设计思想尤其是那些一以贯之的原则。好的语言都有一套完整的原则，比如Java、Ruby和Python。JavaScript却不是这样，所以有人创建了CoffeeScript这门小巧的语言，如果想用Ruby和Python那样写JavaScript，用CoffeeScript就非常的爽，它把语句一对一地编译成JavaScript，编译后的JavaScript秉承了很多的最佳实践，且符合规范，可以通过JavaScript Lint的验证，我前些日子用了三天的业余时间看了《深入浅出CoffeeScript》，然后写了一个小程序，确实非常的爽。不过毕竟CoffeeScript只是JavaScript上的一层简单的封装，理解JavaScript本身还是至关重要的。

好吧，言归正传，JavaScript里需要记住的东西多且凌乱，但也不是无迹可寻，我觉得记住以下几个关键点足以，更多的内容在实践中不断深入理解即可。

第一点，要记住JavaScript是一门面向对象的语言，JavaScript 中所有变量都是对象，除了两个例外 null 和 undefined。注意数字也是对象，虽然你不能用下面这样的语句来调用：

	2.toString(); // 出错：SyntaxError

类似的写法在Ruby等面向对象语言中是没有问题的，其实，这是JavaScript解析器的一个设计错误，它会把“.”当成小数点，可以用一些变通的写法：

	2..toString(); // 第二个点号可以正常解析

	2 .toString(); // 注意点号前面的空格

	(2).toString(); // 2先被计算

JavaScript定义对象也很简单，可以通过字面量的方式定义一个对象：

	var o = {name:'wang'}

	o.name;  //->  wang

	o.age = 20;

可以看出来，对象就像是一个希哈表一样简单。当然还有其它的定义方式，将在后面讲解。

注1：以上内容主要来自《JavaScript 秘密花园》。

注2：有人把数字和字符串这些JavaScript内置的类型叫做伪对象，那时从实现方式上来说的，使用者基本不需要关心这一点。

第二点，要记住JavaScript中函数是一等公民。比如在Java这样的基于类的编程语言中，每个方法不能独立存在，必须定义在某个类中。而Javascript中是可以单独定义函数的:

	var func = function(){

	        //do something...

	};

	func();  //调用

第一点里说一切都是对象，可想而知，函数也是对象，只不过是个比较特殊的对象，比如可以调用，当然他的特殊性还不止于此，后面还会讲到。既然是对象，那么它就可以被作为其他函数的参数传递，也可以作为另外一个对象的属性。

JavaScript里也有this这个关键字，但是上面说过，函数可以独立地存在，那么这个this并不像Java中那样指的是方法的拥有者（对象）。其实this只是函数执行的上下文对象，this的同义词是“这个方法的执行上下文”而不是“这个方法所属的对象”。要弄清这一点需要理解函数的call方法：

	var func = function(param){

	         console.log(this.name);

	         console.log(param);

	};

可以用下面的方式调用函数：

	func();

输出：

	undefined

	abc

也可以使用call来调用：

	var o = {name:'wang'};

	func.call(o,'abc');

输出：

	wang

	abc

可以看出this就是call的第一个参数。上面的调用等同于

	var o = {name:'wang'，f1:func};

	o.f1('abc')

注1：call是定义在Function.prototype上的一个方法，apply方法可以实现同样的功能，只是参数不一样而已。当然call和apply还有不一样的地方，请参看《深入浅出CoffeeScript》22页的解释。

第三点，要记住JavaScript不是基于类的面向对象语言，它是基于原型的。现在的主流的面向对象的编程语言都是基于类来设计的，所以很多人难以理解一门面向对象语言里为什么会没有类。但实际上基于类还是基于原型（Prototype）都只是面向对象编程语言的不同的实现风格。

除了用字面量来创建对象，也可以通过new关键字来创建：

	function MyFunc(){

	        this.name='wang'

	};

	var o = new MyFunc();

MyFunc是一个函数，它本质上和普通函数没有区别，只是用途不同而已，这样和new关键字配合使用用来创建对象的函数通常叫做构造函数，首字母一般大写（这不是强制的）。不能用类创建对象的方式来理解new这个用法,当看到new Object这样的代码时也就会明白Object只是一个函数而不是一个类了，《深入浅出CoffeeScript》里有一句话说到很好：

> The new keyword says,  “Don’t  return  the  result  of  the  function;  instead,create a new object, run the function in that object ’s context, and then return the object.” 

当然还不止于此，function对象有一个prototype属性，当然它的值是一个对象。使用这个function创建出的对象会有一个constructor属性指向这个function，同时，还会有一个隐藏的属性_proto_（在某些浏览器里属性名称是_proto_且暴露出来可以访问，有些浏览器里不叫这个名字且无法访问）指向function的prototype属性，关键是当访问这个对象的某个 属性时，如果对象本身没有这个属性，那么会访问它的_proto_属性指向的对象，看它是否有这个要访问的属性，没有的话会继续顺着_proto_找上去，这就是通常所说的对象的原型链查找。ECMA标准中是这样表述的：

> every object created by that constructor has an implicit reference to the prototype (called the object's prototype) associated with its constructor

在上面的例子中，如果在MyFunc.prototype中加入一些函数属性，那么用MyFunc创建出的任何对象都可以用“.”来直接访问这些函数了，这样MyFunc就更像Java中的类了，但我们要记住，其实在JavaScript了是没有类的，这是一种模拟：

	function MyFunc(){

	        this.name='wang'

	};

	MyFunc.prototype.func1=function(){console.log(this.name);};

	var o = new MyFunc();

	o.func1();

如上所述，原型链是可以不断向上追溯的，所以也可以模拟实现继承，但这是更高级的主题了，而且在我的实践中几乎都没有遇到需要继承的功能。如果是在写界面相关的程序，jQuery提供了extend方法，虽然不是基于原型的继承，但一般也够用。如果写独立于界面之外的复杂逻辑，我宁愿使用CoffeeScript来做，它提供了class和extends等语法。另外，像backbonejs等框架都提供了继承的语法，一般来说都不需要我们自己去实现。

最后总结一下，其实需要记住的几点都是关于对象的，JavaScript的面向对象系统是如此奇特，似乎每一个想要了解它的人都会写上几篇相关的文章，比如酷壳的[《Javascript 面向对象编程》](http://coolshell.cn/articles/6441.html)和[《再谈javascript面向对象编程》](http://coolshell.cn/articles/6668.html)以及阮一峰的[《Javascript 面向对象编程》](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html)和[《Javascript定义类（class）的三种方法》](http://www.ruanyifeng.com/blog/2012/07/three_ways_to_define_a_javascript_class.html)。其实[《JavaScript中的原型和对象机制》](http://www.cnblogs.com/FlyingCat/archive/2009/09/21/1570656.html)讲的更深入些，而《JavaScript对象系统深入剖析 [1](http://blog.csdn.net/adwu73/article/details/7219044),[2](http://blog.csdn.net/adwu73/article/details/7219887),[3](http://blog.csdn.net/adwu73/article/details/7224356)》这一系列文章浅显易懂，分析了浏览器中JavaScript引擎中的对象系统。[《深入理解JavaScript系列》](http://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html)也有相关的文章。可以进一步延伸阅读。道理都是一个，但每一个人的理解方式都不一样，写下来，不但方便其他人，更主要是为了加深自己的理解和记忆。


{% include references.md %}
