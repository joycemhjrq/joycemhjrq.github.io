---
layout: post
title: Selenium 生成的classpath文件
categories: Selenium
tags: classpath
---

1、.classpath文件时eclipse新建web工程的时候生成的一个文件，改变java Build path的时候或者改变加载的jar包顺序的时候，这个文件也会改变。

2、.classpath文件不能随意删除。个人在将已有的工程import进入eclipse工作区的时候将.classpath文件删除了，然后再重新导入jar包，整个工程就会重新编译，也就会产生.classpath文件，但是这个重新生成的.classpath文件的内容可能就会出问题，也就有可能导致在tomcat部署的时候出现不可预料的问题。

例如：

原始的.classpath文件部分主要内容如下：

    <classpathentry kind="src" path="src"/>
    <classpathentry kind="con" path="org.eclipse.jdt.launching.JRE_CONTAINER"/>
    <classpathentry kind="output" path="WebRoot/WEB-INF/classes"/>

个人删除后，eclipse重新build的.classpath文件部分主要内容如下：

    <classpathentry kind="src" path=""/>
    <classpathentry kind="con" path="org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/jdk1.6.0_11"/>
    <classpathentry kind="output" path="/bin"/>
    说明下：<classpathentry kind="src" path="src"/> 表示工程源文件的地址；
    <classpathentry kind="output" path="/bin"/>表示产生的class文件地址；

比较前后两个.classpath文件可以发现，重新build的.classpath文件里面的源文件地址为 <classpathentry kind="src" path=""/>，这样，在整个工程里面就会出现所有package错误，此时eclipse默认为package src.com.test, 而工程里面正确的包名应该是 package com.test

另外，如果新生成的.classpath文件class文件地址为bin目录下的，即<classpathentry kind="output" path="/bin"/> ，tomcat部署工程的时候，默认去WebRoot/WEN-INF/classes这个目录查找class文件，而此时class文件去不存在于bin目录下面，故而tomcat下面没有class文件，在启动时也就会产生ClassNotFoundException错误！

如果碰到以上问题的话，则可以参考原始.classpath文件修改过来就可以了

以前没有注意过.classpath文件，这次让我深刻了解了它，代价比较严重，花了不少时间。现在想想归结原因就是我把.classpath文件轻易删除了。