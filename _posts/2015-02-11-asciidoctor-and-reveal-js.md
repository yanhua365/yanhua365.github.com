---
layout: post
title:  使用Asciidoctor和reveal.js制作炫酷的幻灯片
tags:   blogging
image:  asciidoctor.png
---

[AsciiDoc](http://asciidoctor.org/docs/what-is-asciidoc/) 是又一种 `lightweight markup languages`，它比Markdown提供了更多的元素，这些元素提供的语义往往是出版写作里常用的，而且也很容易输出为HTML、PDF等多种格式，从这一点来看，AsciiDoc是用来替换DocBook的，非常适合写书，尤其是技术文档和指南，现在Spring Framework的文档全部用AsiiDoc来写作了(以前他们用的是DocBook)。
`lightweight markup languages`的一个核心理念就是**内容为王**，也就是你只关心你写的内容和结构而不用去关心
它的展现，对于我们这些美术基础极差但思维逻辑严谨的程序员来说这样是最好不过的做法了。
[reveal.js](http://lab.hakim.se/reveal-js/)在现在流行的多个HTML5的slide里也是最炫酷的，而且有几个
功能非常实用（后面我们会讲到）。
但是，用HTML去写一个reveal.js的一个slide需要写很多的标签还是挺难也挺繁琐的。如果我们能用AsciiDoc去写的话就简单很多了。


{{ more }}

### 目录
{:.no_toc}

* Table of Contents Placeholder
{:toc}

-----

## 安装Asciidoctor

[Asciidoctor](http://asciidoctor.org/) 是一个用Ruby写的AsciiDoc的解析器，所以安装前需要安装[Ruby](https://www.ruby-lang.org/zh_cn/) ，在Windows上推荐使用[RubyInstaller](http://rubyinstaller.org/)来安装，本次安装的是1.9.3版本。

然后可以安装Asciidoctor了：

     gem install asciidoctor

如果不能安装可以先[下载gem]( https://rubygems.org/gems/asciidoctor )然后本地，用下面的命令安装：

    gem install --local asciidoctor-1.5.2.gem

这样就可以了，写一个`README.adoc`内容如下：

    = Hello, AsciiDoc!
    Doc Writer <doc@example.com>

    An introduction to http://asciidoc.org[AsciiDoc].

    == First Section

    * item 1
    * item 2

    [source,ruby]
    puts "Hello, World!"

在命令行运行：

    asciidoctor README.adoc

就可以生成一个README.html文件了。


## 用Asciidoctor制作幻灯片

### 安装asciidoctor-reveal.js

在Asciidoctor下有个子项目[asciidoctor-reveal.js](https://github.com/asciidoctor/asciidoctor-reveal.js) 可以利用AsciiDoc的语法制作reveal格式的幻灯片。

首先需要安装slim：

    gem install slim --version 2.1.0

同样，如果无法安装的话可以通过本地安装它和它的依赖:

    gem install --local tilt-2.0.1.gem
    gem install --local temple-0.6.6.gem
    gem install --local slim-2.0.1.gem

### 创建slide项目

创建一个幻灯片项目：

    git clone git://github.com/asciidoctor/asciidoctor-reveal.js.git

然后，在这个目录下建一个adoc文档，比如`index.adoc`：

    = Title Slide

    == Slide One

    * Foo
    * Bar
    * World

    == Slide Two

    Hello World - Good Bye Cruel World

    [NOTE.speaker]
    --
    Actually things aren't that bad
    --


然后在命令行执行：

    asciidoctor -T templates/slim index.adoc

这样就生成一个index.html文件。

但这时候还不能运行，因为还没有reveal.js的文件，在当前目录用git下载一个：

     $ git clone -b 3.0.0 git://github.com/hakimel/reveal.js.git

这样就可以了。


如果想动态改变皮肤可以在js控制台里执行：

    document.getElementById('theme').setAttribute('href','reveal.js/css/theme/sky.css')

更多的语法的介绍参考[asciidoctor-reveal.js](https://github.com/asciidoctor/asciidoctor-reveal.js)的项目文档吧。

### 示例

我做了一个[介绍ReviewBoard的幻灯片](http://yanhua365.com/slide-reviewboard/)，里面涵盖了常用的一些功能，可以[查看源代码](https://github.com/yanhua365/slide-reviewboard)来学习这些常用的特性。
你也可以直接clone这个项目，在此基础上修改制作自己的幻灯片:

    git clone https://github.com/yanhua365/slide-reviewboard.git


## reveal.js最好用的几个特性

除了炫酷的效果和多样的皮肤外，reveal.js下面几个特性我觉得也十分实用。

### 多级结构

用两个等号开头(`==`)代表一张幻灯片的开始，比如：

    == Review Board功能概览

    是一套用python编写的用于代码审查的Web程序，支持以下的浏览器：

    * Firefox 3.0+
    * Internet Explorer 9+
    * Google Chrome

在演示的时候多张幻灯片可以通过左右箭头来切换。reveal.js独特的地方在于他支持两级目录结构，如果在上面的幻灯片下紧接着放一个三个等号开头的幻灯片，那么代表它的下级页面或者代表了更详细的信息，在演示的时候用向下的箭头才能导航到它。比如：

    [data-background="green"]
    === 任务列表

    image::img/task-list.png[审查任务列表]


这样做的好处是结构清晰，详略得当，在演示的时候可以根据时间等实际情况来觉得是否演示这些细节页面。

> 在这些细节页面上，我使用了`[data-background="green"]`来为这个细节页面指定了一个不同的背景，在演示的时候这有利于提醒观众我们在进行更细节的展示。


### 注释功能

使用`[NOTE.speaker]`可以指定下面的内容是本张幻灯片的注释，注释在演示时观众是看不到的。


    == 代码审查

    [, Yahoo! Web Search]
    ""
    Review Board 已经改变了代码评审的方式，其可以强迫高质量的代码标准和风格，并可以成为程序员编程的指导者。
    当你访问search.yahoo.com 时，其代码都是使用 Review board工具Review过的。
    ""

    [NOTE.speaker]
    --
    雅虎搜索使用Review Board作为他们的代码审查工具。
    --

但演示者可以通过在页面上按一下`s`键，弹出一个窗口看到这些注释：

![查看注释的窗口](/img/posts/revealjs-notes-demo.png)


除了能看到本张幻灯的内容和注释，还能看到一个计时器以及下一张幻灯的缩略图。


### 其他功能

还有很多其它实用的小功能，比如可以用`[%step]`来指定列表是一步步显示的，可以通过`ESC`键查看所有幻灯片的大纲概览等等。
