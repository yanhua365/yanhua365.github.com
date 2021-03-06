---
layout: post
title:  安装优麒麟操作系统和常用的软件
tags:   blogging
image:  gcal-backup.png
---

最近在使用Git，经常会用到Git的命令行，发现开发时很多事情用命令行比图形化界面要高效的多，所以突然想给自己的电脑里安装个linux系统，
看能不能把开发环境完全转换到linux上来，下面是安装的过程。



{{ more }}


### 系统安装

从 [优麒麟官网上](http://www.ubuntukylin.com/) 下载14.04的ISO文件，然后下载`LinuxLive USB Creator 2.9.3.exe`制作U盘安装盘，按说明文档安装就可以了。


### 安装无线网卡驱动

Ubuntu的无线网络特别的慢，特别的不稳定。可以更新驱动程序。

在 `系统设置->软件和更新->附加驱动` 选择 `bradcom 802.11` 的那个专有驱动，点击"应用更改"按钮等待下载安装即可。


### 安装Git

    sudo apt-get install git-core

然后配置Git：

    git config --global user.name "yanhua365"
    git config --global user.email "yanhua365@foxmail.com"

生成并配置SSH Key请参考 [GitHub上的帮助](https://help.github.com/articles/generating-ssh-keys/)。

### Java开发环境的安装

从[Java的官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)上下载 `jdk-8u45-linux-x64.tar.gz` 解压，`tar xfz jdk-8u45-linux-x64.tar.gz`，
然后拷贝 ` sudo mv ./jdk1.8.0_45 /usr/local/jdk1.8.0_45`

在` ~/.profile` 文件里添加：

    export JAVA_HOME=/usr/local/jdk1.8.0_45
    export PATH=$JAVA_HOME/bin:$PATH

然后重启，JAVA就生效了。


maven的安装方式与此类似。

安装Intellij IDEA：

    tar xfz ./ideaIU-14.1.3.tar.gz
    sudo mv ./idea-IU-141.1010.3 /usr/local/idea-14

由于是IDEA快捷键的重度用户，所以需要把系统里和IDEA冲突的快捷键都禁止掉。除了在系统里设置外，还需要在软件中心安装`Unity Tweak Tool`，另外输入法里也把快捷键都改成和IDEA不冲突的。

### 安装nodejs

[下载](https://nodejs.org/)最新的nodejs(下载binary的版本)，然后解压：

    tar xfz node-v0.12.4-linux-x64.tar.gz

移动到合适的目录：

    sudo mv ./node-v0.12.4-linux-x64 /usr/local/node-v0.12.4-linux-64

然后，设置连接：

    sudo ln -s /usr/local/node-v0.12.4-linux-64/bin/node /usr/local/bin/node
    sudo ln -s /usr/local/node-v0.12.4-linux-64/bin/npm /usr/local/bin/npm


这样就可以了。
为了使用国内的淘宝的源，可以安装他们的cnpm：

    sudo npm install -g cnpm --registry=https://registry.npm.taobao.org

以后就可以运行cnpm代替npm命令了。详细参考[官网说明](http://npm.taobao.org/)

### 安装atom
直接下载atom的deb包，双击就可以安装。编辑中文会出现乱码，查看一下系统里安装了什么中文字体：

    fc-list :lang=zh

比如发现安装了`方正黑体_GBK`，那么，在`Setting`里把`Font Famliy`设置为`DejaVu Sans Mono,方正黑体_GBK`即可。

### 安装Docky

在unbuntu软件中心安装即可。

### 安装svn

    sudo apt-get install subversion subversion-tools
    sudo apt-get install git-svn

### 安装GVM

    curl -s get.gvmtool.net | bash

安装成功后，按提示打开新窗口，就可以使用gvm了，比如安装groovvy：

    gvm install groovy

更多的使用方法请看： http://gvmtool.net/


### 安装Asciidoctor

如果还没有安装ruby，先安装：

    sudo apt-get install ruby

然后：

    sudo gem install asciidoctor

这样就可以了。
