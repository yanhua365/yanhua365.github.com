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
但是，用HTML去写一个reveal.js的一个slider需要写很多的标签还是挺难也挺繁琐的。如果我们能用AsciiDoc去写的话就简单很多了。


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

     git clone git://github.com/hakimel/reveal.js.git

但这时还是不能正常运行，这是因为最新的reveal.js较之前有了一些变化，asciidoctor-reveal.js还没有做相应的改动，需要：

- `reveal.js\js\reveal.js`复制一份为`reveal.js\js\reveal.min.js`

- `reveal.js\css\reveal.css`复制一份为`reveal.js\css\reveal.min.css`

- `reveal.js\css\theme\black.css`复制一份为`reveal.js\css\theme\default.css`

这样就可以了。


如果想动态改变皮肤可以在js控制台里执行：

    document.getElementById('theme').setAttribute('href','reveal.js/css/theme/sky.css')

更多的语法的介绍参考[asciidoctor-reveal.js](https://github.com/asciidoctor/asciidoctor-reveal.js)的项目文档吧。

### 示例

下面是一个我做的介绍ReviewBoard的幻灯片，可以参考：

    = Review Board介绍
    :source-highlighter: highlight.js

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



    == Review Board功能概览

    是一套用python编写的用于代码审查的Web程序，支持以下的浏览器：

    * Firefox 3.0+
    * Internet Explorer 9+
    * Google Chrome



    [data-background="green"]
    === 任务列表

    image::img/task-list.png[审查任务列表]

    [NOTE.speaker]
    --
    这里能看到所有的任务
    --

    [data-background="green"]
    === 文件比较

    image::img/diff.png[文件比较]

    [data-background="green"]
    === 提交注释

    image::img/comment.png[提交注释]

    [data-background="green"]
    === Issue汇总

    image::img/issue-summary.png[Issue汇总]

    [data-background="green"]
    === 历史记录

    image::img/history.png[历史记录]

    == 代码评审的两种类型

    [%step]
    * Pre-commit review
    * Post-commit review

    [data-background="green"]
    === Pre-commit review

    ""
    在代码提交到代码库之前审查。先往Review Board里上传一个diff文件，这样审查者可用对其进行评论，一旦批准，代码会被提交到代码库中。
    ""

    [data-background="green"]
    === Post-commit review

    ""
    代码先提交到代码库中，在之后的某个时间点进行代码审查。发现的问题修改的代码后需要重新提交到代码库中。
    ""

    [data-background="green"]
    === 优缺点比较

    ""
    pre-commit 的好处是在代码入库前就能发现错误，从而避免引起线上产品的问题。缺点就是导致开发时间变长，尤其是在项目紧急的时候比较难贯彻执行。
    ""

    == pre-commit review的流程

    . 对已修改的代码创建一个review请求，上传diff文件,发布这个请求
    . 等待评审人查看并给出反馈
    . 如果评审人提出改进建议，修改本地代码并重新生成diff文件并上传，说明改变的内容，重新发布，跳到第2步
    . 如果评审人执行了"Ship it"，那么就提交代码到仓库，关闭并提交这个请求


    == DEMO
