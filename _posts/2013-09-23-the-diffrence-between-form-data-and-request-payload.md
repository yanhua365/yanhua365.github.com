---
layout: post
title: HTTP请求中的form data和request payload的区别
category: development
---

今天帮朋友调试一个程序时发现用jQuery的ajax方法和post方法分别发送请求，在后台Servlet进行处理时结果是不一样的，比如用$.ajax方法发送请求时（data参数是一个JSON.stringify()处理后的字符串，而不是一个JSON对象），servlet里可以这样使用Gson来解析：

	new Jsonparser().parse(request.getReader())

但此时是不可用request.getParam(key) 来取值的。

如果用$.post方法来发送请求（data参数是一个JSON对象,而不要再用JSON.stringify()处理为字符串了），结果恰恰相反。

在Chrome中调试发现，$.ajax发送的请求显示在request payload下面，而使用$.post方法发送的请求显示在form data下面。有什么区别呢？在万能的stackoverflow上找到答案了，有人问了这个问题[What is the difference between form data and request payload?](http://stackoverflow.com/questions/10494574/what-is-the-difference-between-form-data-and-request-payload)：

>When I send an AJAX Post request and send parameters in queryString in send() method,Chrome Developer Tool's XHR capture tool shows the parameters under request payload. and when I use jquery's post function, The tool shows parameters under Form Data section.

>What is the difference ?


回答是：

>you have not provided enough information how you use the send function, but I assume that you do not set mime type to specify you are sending form data

	xhr.setRequestHeader("Content-type","application/x-www-form-urlencoded");

>the data sent are in this case encoded as you encode a query string

	xhr.send("name=foo&value=bar");

>otherwise it will not be interpreted as form data by Developer Tools.
>jquery does majority of work for you in this regard.


关键就是设置Content-type这个Header为application/x-www-form-urlencoded，实际上对于常规的HTML页面上的form的Content-type默认就是这个值。

相似的问题还发生在AngularJS的$http方法中，[How can I make angular.js post data as form data instead of a request payload?](http://stackoverflow.com/questions/11442632/how-can-i-make-angular-js-post-data-as-form-data-instead-of-a-request-payload) 这个问题竟然有77个“顶”，看来遇到此问题的人还真不少。

*注：这个问题里说jQuery的ajax方法是可以的，我今天遇到是不可以的，这个需要再验证一下。*

当然解决的方法是一样的：

	$http({
	    method: 'POST',
	    url: url,
	    data: xsrf,
	    headers: {'Content-Type': 'application/x-www-form-urlencoded'}
	})

ArgularJS的$http方法还支持全局设置：

	$http.defaults.headers.post["Content-Type"] = "application/x-www-form-urlencoded";

还有人专门针对这个问题写了篇博客，分析了原因[Make AngularJS $http service behave like jQuery.ajax()](http://victorblog.com/2012/12/20/make-angularjs-http-service-behave-like-jquery-ajax/)

>The difference is in how jQuery and AngularJS serialize and transmit the data. Fundamentally, the problem lies with your server language of choice being unable to understand AngularJS’s transmission natively—that’s a darn shame because AngularJS is certainly not doing anything wrong. By default, jQuery transmits data using Content-Type: x-www-form-urlencoded and the familiar foo=bar&baz=moe serialization. AngularJS, however, transmits data using Content-Type: application/json and { "foo": "bar", "baz": "moe" } JSON serialization, which unfortunately some Web server languages—notably PHP—do not unserialize natively.

当然文章了给出了另外的一种处理方案，还没细读，这个问题先记录在这里，以后再做些例子来详细研究一下。

另外,如果做Server端的API，默认支持哪种请求为好呢？