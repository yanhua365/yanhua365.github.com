---
layout: post
title: 在Groovy中使用Ant和Subversion
category: development
---

在Groovy中使用Ant
-------------------------------





在Groovy中使用SvnAnt
--------------------------------------

```
<project default="checkout-var1" basedir=".">
 
    <property name="checkout.url" value="http://localhost:9000/svn/testrepo" />
    <property name="checkout.dest" value="./target" />
    <property name="svn.username" value="christoph" />
    <property name="svn.password" value="topfsecret" />
    <property name="lib.dir" value="./libs" />
 
    <path id= "svn.classpath">
        <fileset dir= "${lib.dir}">
            <include name="svnant-1.4dev.jar" />
            <include name="svnClientAdapter-1.7.0.jar" />
            <!-- latest svnkit-1.7-libs from org.tmatesoft.svn_1.7.10.standalone -->
            <include name="svnkit-1.7.10.jar" />
            <include name="svnkit-javahl16-1.7.10.jar" />
            <include name="trilead-ssh2-1.0.0-build217.jar" /><!-- aka ganymed.jar -->
            <include name="sqljet-1.1.7.jar" />
            <include name="sequence-library-1.0.2.jar" />
            <include name="antlr-runtime-3.4.jar" />
        </fileset>
    </path>
 
    <!-- typedef for svnant -->
    <typedef resource="org/tigris/subversion/svnant/svnantlib.xml" classpathref="svn.classpath" /> 
 
    <svnSetting
        client="svnkit"
        id="svn.settings"
        username="${svn.username}"
        password="${svn.password}"
     />
 
    <target name="checkout">
        <mkdir dir="${checkout.dest}" />
        <svn refid="svn.settings">
            <checkout url="${checkout.url}" destPath="${checkout.dest}" />        
        </svn>
        <!-- get the working-copy-version of the checkout -->
        <loadfile property="svn.wc.format" srcFile="${checkout.dest}/.svn/format"/>
        <echo message="format of working-copy: ${svn.wc.format} (7=SVN-1.3, 8=SVN-1.4, 9=SVN-1.5, 10=SVN-1.6, 12=SVN-1.7)" />
    </target> 
 
    <!-- some words about versions: 
    - Lastest stable version of SVNKit (1.7.10) is NOT compatible with Subversion 1.8 working copy format. It it compatible with Subversion 1.8 servers.
    - svnant-1.3.1 is bound against subversion 1.6.x, svnant-1.4 bound against subversion 1.7
    -->
 
</project>
```

http://christoph-burmeister.eu/?p=2261


