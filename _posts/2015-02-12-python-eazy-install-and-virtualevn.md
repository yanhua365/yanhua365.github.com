---
layout: post
title:  使用Python的easy install和virtualevn搭建虚拟环境
tags:   blogging
image:  py-virtualevn.gif
---

作为一个程序员，你总会面对多个不同的项目，他们依赖于不同的运行时环境，往往也依赖不同版本的类库，无论运行时环境还是类库，他们都很难做到百分百的向前兼容，所以，在开发的时候每个工程若能有一个相对隔离的环境将会避免很多兼容性的问题，甚至在线上这样做也会使得部署变得更简单可控。在Java中，我们通过指定不同的JDK版本并使用Maven这样的工具来管理依赖可以在很大程度上保证这种隔离性，那么在Python中有什么办法吗？virtualevn就是用来解决这个问题的，virtualevn不会为你完全复制一个当前Python环境的副本，它是用一种轻量级的方式来实现一个虚拟的环境进而实现隔离的。

下面说明一下如何在Windows环境中用virtualevn搭建一个这样虚拟环境。

{{ more }}

### 安装easy_install和virtualenv

下载 [`ez_setup.py`](http://peak.telecommunity.com/dist/ez_setup.py)，然后用Python执行：

```
D:\program\Python26>python D:\resource\python\ez_setup.py
```

然后安装virtualevn：

```
D:\program\Python26>Scripts\easy_install virtualenv
```
这样就安装完成了，就是这么简单。

### 创建虚拟环境

先创建一个目录，比如`D:\resource\viewbordevn`，在此目录下执行：

```
D:\resource\viewbordevn>D:\program\Python26\Scripts\virtualenv venv
```
创建成功后需要激活这个环境：

```
D:\resource\viewbordevn>venv\scripts\activate
```

这时候你会看到命令行的提示符是` (venv) D:\resource\viewbordevn>`这样的格式，开头的`(venv)`说明这是一个虚拟的环境。以后就在这个命令行下安装其它依赖或执行命令就可以了。
比如我们安装ReviewBord的话就运行：

```
(venv) D:\resource\viewbordevn>easy_install ReviewBoard
```

你可以为一种类型的工程甚至一个工程创建一个。

在Linux和Mac下如何安装，可以参考[Flask的文档](http://flask.pocoo.org/docs/0.10/installation/#virtualenv)，顺便说一句，里面说到的用pip代替easy_install也是一种更好的实践。
