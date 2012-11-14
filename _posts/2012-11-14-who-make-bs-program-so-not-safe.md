---
layout: post
title: 是什么让BS软件变得如此脆弱
category: development
---

一个略懂HTML的朋友告诉我，他在使用公司OA系统时，用Chrome在表单页面上做了点小改动，然后点了保存按钮，本来不能修改的数据竟然修改并保存到数据库里去了，软件真的这么脆弱吗？我看了一下他们的系统，是一个BS架构的企业软件，这个表单的局部类似下面这个样子：

![](http://ww3.sinaimg.cn/mw690/67012a6cjw1dyuufkglnvj.jpg)

可以看到类型这个下拉框是不可编辑的，在Chrome里按下F12可以看到对应的HTML代码是这样的：

	<select id="amountType" name="amountType" disabled="true">
			<option value="1" selected>小额</option>
			<option value="2">大额</option>
	</select>

在Chrom里很容易把它修改成这样：

	<select id="amountType" name="amountType" disabled="true">
			<option value="1">小额</option>
			<option value="2" selected>大额</option>
	</select>

disabled这个属性是修改不了的，如果是一般的表单提交，修改后的值不会提交多服务器，但在这个程序里也不需要修改，点击保存按钮后，JS把表单数据序列化为JSON（包含了amoutType这个下拉框的值），然后用ajax提交了。其实，即使是常规的表单提交也不是没有办法，在Chrome里可以先从DOM里删除select这个下拉框，然后在控制台里输入：

  document.forms[0].appendChild(document.createElement('input')

页面上会多出一个input输入框来，然后再给它添加几个属性就好了：

	<input id="amountType" name="amountType" value="2">

这样的效果是一样的。

像Chrome和FireFox（安装了FireBug）这样的浏览器只要按下F12后，修改HTML就非常容易，但是浏览器里的F12并不是让程序变得脆弱的关键原因，它只是让破坏程序编的更加容易了。即使没有它，不也可以用HTTPClient发出想要的任何请求吗？罪魁祸首是服务器端的程序漏洞。虽然我们无法看到这个OA系统的服务器端代码，但是也可以猜个大概：

![](http://ww2.sinaimg.cn/mw690/67012a6cjw1dyuw7ven51j.jpg)

比如客户端提交的JSON数据，一般的MVC框架（比如Spring MVC）会把它照单全收转化为一个模型对象（比如一个JPA的实体对象），然后ORM会把这个模型对象的全部属性照单全收地保存到数据库中。

问题就出在两个“照单全收”上。其实在Rails这样的框架里早就有提供了update_attributes这样的方法处理这个问题，但是这样需要额外的编码控制，这会增加工作量，尤其是对于页面和功能很多的企业应用软件，精细地控制这两个环节的成本还是很高的，再加上企业应用软件在内网运行用户也不多，钻漏洞的人也少，所以这个问题也不被重视。总之，由于侥幸、偷懒或为了减少开发工作量，很多企业应用的软件都存在这个问题。

当我和其他开发者交流这个问题的时候，他们大都认同这样的漏洞在BS软件里大量存在，但是也大都认为不主动增加工作量控制两个“照单全收”是没有办法的。某种程度上这是对的。

但是换一种架构也许就不一样了，确有一些架构不用做多余的处理就可以避免这个问题。比如状态保存在服务器端的那些MVC框架，当然我并不认为这是目前最好的架构方案，这里只针对这一个问题来说明这种不怎么RESTFUL的框架如何使用更少的工作量实现更高的安全性。下面以JSF为例来说明。

![](http://ww2.sinaimg.cn/mw690/67012a6cjw1dyuxa850rzj.jpg)

在JSF中一个页面对应一颗组件树，它保存在服务器端，每个组件有自己的属性，HTTP请求数据到来时，更新各个组件的value属性，然后，组件的value值再更新到Model对象里，最终Model会被保存到数据库中。如果一个组件（比如图中的selectOneMenu）的属性是disabled的，JSF框架是不会用HTTP中的数据更新这个组件的value属性的。由于组件树上的组件是在服务器端（根据一个服务器端的xhtml模板文件）创建和维护的，客户端几乎是无法改变它的（只有服务器端的Java代码可以把图中的那个selectOneMenu改为非disabled），这样就避免了Model被HTTP数据非法污染。当然，这样也有不好的地方，服务器端需要消耗更多的资源。

最后想说的是如果您看到了这篇文章，不要用这个方法在任何正式的系统上去试验，文章的目的是让更多的开发者避免这样的漏洞出现，当然文章开头提到的那位朋友也是虚构的。

{% include references.md %}
