---
layout: post
title: Maven 使用maven ant task实现非标准打包
categories: Maven
tags: maven
---

[原文](http://www.blogjava.net/aoxj/archive/2008/12/29/248939.html)

maven很强大，但是总有些事情干起来不是得心应手，没有使用ant时那种想怎么干就怎么干的流畅感。尤其当要打包一个特殊（相对maven的标准架构而且）时，常有不知所措的感觉。当然这个应该和自己对maven的了解不够有关，毕竟，“初学maven”嘛。

但是maven在依赖管理方面实在是太强大了，太喜欢，退回原来的ant方式完全不可能，我想用过maven的人，一般是不会有回到原来在cvs,subversion中checkin/checkout n个jar包的时代，仅此一项理由就足够继续坚持使用maven了。

然而ant的灵活又难于忘怀，尤其是从ant的build.xml一路走来的人，总是回不知不觉间想到ant的美好。鱼与熊掌，我都想要。最近想打包一个java应用，很简单但即不是标准的j2ee appication也不是web application, 用maven完全不知道该怎么打包，package出来的完全不是我想要的，在网上四处google maven资料的时候，总有用回ant拉倒的冲动。

先交代一下背景吧，我要打包的程序，是这个样子：

    demo1
     |____lib
     |_____demo1.jar
     |_____*****.jar
     |_____*****.jar
     |____config
     |_____*****.properties
     |_____*****.xml
     |____log
     |_____*****.log
     |____run.bat
     |____run.sh

这个应用的打包模式很简单，一个bat或者sh脚本用来执行，lib目录下存放所有的jar包，包括自己的源代码编译打包的jar和第三方包。config下是配置文件，这些文件需要在安装时或者运行前修改，比如监听的端口啊，数据库信息之类的。log目录存放日志文件。最后打包的产物是一个zip包（或者tar,tar.gz)。

遇到的问题，就是maven标准的打包方式中根本不考虑类似的情况，什么jar，ear，war完全不适用。而且maven有些理念也诧异，比如maven标准的config目录是src/main/config，但是这个目录里面的配置文件默认会打包到jar包中，晕，都在jar里面了还让人怎么改啊？

本着尽量只用maven不用ant的想法，我在maven的资料中看了好久，没有找到解决的方法。晕，难道大家都只打包标准的ear，jar，war，只有我这样无聊的人才会有这种打包的需求？

几经寻觅和探索，最后发现，maven ant tasks似乎是一个不错的选择。带着mavenanttasks的官方文档和google上搜到的几篇文章，开始尝试，成功实现功能。现在将过程和方法share给大家。

首先建立java项目anttaskdemo1，按照maven的推荐，文件结构如下：

    anttaskdemo1
     |____src/main/java
     |____src/main/config
     |____src/main/bin
     |____src/main/resources
     |____src/test/java
     |____src/test/resources
     |____target
     |____build.properties
     |____build.xml
     |____pom.xml

其中src/main/java下放java代码；src/main/resources下放一个*.properties文件，这个资源文件是打包到jar中，内容打包之后不需要改变的。src/main/config下放一个标准的log4j.xml，这个是有在安装运行前临时修改的需要的。src/main/bin下放置可执行文件。

1.首先看pom.xml，标准内容，很简单，象征性的加入几个依赖

{% highlight xml %} 

<dependency>
 <groupId>commons-codec</groupId>
 <artifactId>commons-codec</artifactId>
 <version>1.3</version>
</dependency>
<dependency>
 <groupId>log4j</groupId>
 <artifactId>log4j</artifactId>
 <version>1.2.13</version>
 <scope>compile</scope>
</dependency>
<dependency>
 <groupId>org.testng</groupId>
 <artifactId>testng</artifactId>
 <version>5.8</version>
 <scope>test</scope>
 <classifier>jdk15</classifier>
</dependency>

{% endhighlight %}

其中commons-codec，log4j是需要打包到lib中的，运行时需要。testng（或者更一般的junit）是用来单元测试的，不需要打包到lib中。请注意它的设置"<scope>test</scope>"。

2.然后看看build.properties文件，这个内容不多：
    
    M2_REPO=G:/soft/maven/localRepository

    path.package=package
    path.target.name=anttaskdemo1
    path.package.lib=lib
    path.package.log=log
    path.package.config=config
    path.package.bin=

     
     M2_REPO稍后再详谈，后面的path.package之类的是最终打包时使用的几个目录名称，对应于最终打包的结构。

3.build.xml，这个内容比较多。

{% highlight xml %}

<?xml version="1.0" encoding="UTF-8"?>
<project name="demo" default="all" xmlns:artifact="urn:maven-artifact-ant">
     <description>
     </description>
     <property file="build.properties" />
     <target name="init_maven">
         <!--
        remember to set M2_REPO before use this build.xml, for example in eclispe:
         "Window→Preferences→Ant→Runtime", add a new property named "M2_REPO" and set it value point to the path of your maven
         local repository; Or you can set it in build.properties.  You need do one (and only one) of them.
         -->
         <path id="maven-ant-tasks.classpath" path="${M2_REPO}/org/apache/maven/maven-ant-tasks/2.0.9/maven-ant-tasks-2.0.9.jar" />
         <typedef resource="org/apache/maven/artifact/ant/antlib.xml" uri="urn:maven-artifact-ant" classpathref="maven-ant-tasks.classpath" />
         <artifact:pom id="maven.project" file="pom.xml" />
         <artifact:dependencies pathId="classpath.build" filesetid="maven.fileset.build">
             <pom refid="maven.project" />
         </artifact:dependencies>
         <artifact:dependencies pathId="classpath.runtime" filesetid="maven.fileset.runtime" useScope="runtime">
             <pom refid="maven.project" />
         </artifact:dependencies>
     </target>
     <target name="all" depends="init_path, compile, jar, package, zip" description="do all">
         <echo>begin to do all target to build the result package.</echo>
     </target>
     <target name="maven_info" depends="init_maven">
         <echo>Maven build directory is ${maven.project.build.directory}</echo>
         <echo>Maven build finalName is ${maven.project.build.finalName}</echo>
         <echo>Maven build sourceDirectory directory is ${maven.project.build.sourceDirectory}</echo>
         <echo>Maven build outputDirectory directory is ${maven.project.build.outputDirectory}</echo>
         <echo>Maven build scriptSourceDirectory directory is ${maven.project.build.testSourceDirectory}</echo>
         <echo>Maven build testOutputDirectory directory is ${maven.project.build.testOutputDirectory}</echo>
         <echo>Maven build scriptSourceDirectory directory is ${maven.project.build.scriptSourceDirectory}</echo>
         <echo>Maven build resourceDirectory directory is ${maven.project.build.resources}</echo>
         <property name="target.jar.name" value="${maven.project.build.directory}/${maven.project.build.finalName}.jar" />
         <echo>Maven build scriptSourceDirectory directory is ${target.jar.name}</echo>
     </target>
     <target name="clean" depends="init_maven">
         <echo>clean build directory : ${maven.project.build.directory}</echo>
         <delete dir="${maven.project.build.directory}" includes="**/*" />
     </target>
     <target name="init_path" depends="maven_info, clean">
         <echo>make dir for java compile: ${maven.project.build.outputDirectory}</echo>
         <mkdir dir="${maven.project.build.outputDirectory}" />
     </target>
     <target name="compile" description="description" depends="init_maven">
         <javac srcdir="${maven.project.build.sourceDirectory}" destdir="${maven.project.build.outputDirectory}" classpathref="classpath.build" />
     </target>
     <target name="copyResource" depends="init_maven">
         <copy todir="${maven.project.build.outputDirectory}">
             <fileset dir="src/main/resources">
             </fileset>
         </copy>
     </target>
     <target name="jar" depends="compile, copyResource">
         <delete file="${maven.project.build.directory}/${maven.project.build.finalName}.jar"  failonerror="false" />
         <jar destfile="${maven.project.build.directory}/${maven.project.build.finalName}.jar" basedir="${maven.project.build.outputDirectory}">
         </jar>
     </target>
     <target name="package" depends="package_prepare, copyLib, copyConfig, copyBin">
     </target>
     <target name="package_prepare" depends="init_maven">
         <echo>clean package directory : ${maven.project.build.directory}/${path.package}</echo>
         <delete dir="${maven.project.build.directory}/${path.package}" />
         <mkdir dir="${maven.project.build.directory}/${path.package}" />
         <mkdir dir="${maven.project.build.directory}/${path.package}/${path.target.name}" />
         <mkdir dir="${maven.project.build.directory}/${path.package}/${path.target.name}/${path.package.lib}" />
         <mkdir dir="${maven.project.build.directory}/${path.package}/${path.target.name}/${path.package.config}" />
         <mkdir dir="${maven.project.build.directory}/${path.package}/${path.target.name}/${path.package.log}" />
     </target>
     <target name="copyLib" depends="init_maven">
         <copy todir="${maven.project.build.directory}/${path.package}/${path.target.name}/${path.package.lib}">
             <fileset refid="maven.fileset.runtime" />
             <mapper type="flatten" />
         </copy>
         <copy todir="${maven.project.build.directory}/${path.package}/${path.target.name}/${path.package.lib}"
             file="${maven.project.build.directory}/${maven.project.build.finalName}.jar">
         </copy>
     </target>
     <target name="copyConfig" depends="init_maven">
         <copy todir="${maven.project.build.directory}/${path.package}/${path.target.name}/${path.package.config}">
             <fileset dir="src/main/config">
             </fileset>
         </copy>
     </target>
     <target name="copyBin" depends="init_maven">
         <copy todir="${maven.project.build.directory}/${path.package}/${path.target.name}/${path.package.bin}">
             <fileset dir="src/main/bin">
             </fileset>
         </copy>
     </target>
     <target name="zip" depends="init_maven">
         <zip destfile="${maven.project.build.directory}/${path.package}/${path.target.name}.zip">
             <fileset dir="${maven.project.build.directory}/${path.package}/${path.target.name}"></fileset>
         </zip>
     </target>
</project>

{% endhighlight %}

一步一步来讲吧：

3.1 最重要的一步，init_maven

{% highlight xml %}

<target name="init_maven">
     <!--
    remember to set M2_REPO before use this build.xml, for example in eclispe:
     "Window→Preferences→Ant→Runtime", add a new property named "M2_REPO" and set it value point to the path of your maven
     local repository; Or you can set it in build.properties.  You need do one (and only one) of them.
     -->
     <path id="maven-ant-tasks.classpath" path="${M2_REPO}/org/apache/maven/maven-ant-tasks/2.0.9/maven-ant-tasks-2.0.9.jar" />
     <typedef resource="org/apache/maven/artifact/ant/antlib.xml" uri="urn:maven-artifact-ant" classpathref="maven-ant-tasks.classpath" />
     <artifact:pom id="maven.project" file="pom.xml" />
     <artifact:dependencies pathId="classpath.build" filesetid="maven.fileset.build">
         <pom refid="maven.project" />
     </artifact:dependencies>
     <artifact:dependencies pathId="classpath.runtime" filesetid="maven.fileset.runtime" useScope="runtime">
         <pom refid="maven.project" />
     </artifact:dependencies>
</target>

{% endhighlight %}

在ant中使用maven-ant-tasks，就需要装载maven-ant-tasks的jar包，方法有两种，一种是直接将maven-ant-tasks-2.0.9.jar放到ant的lib下，自然就可以直接使用。但是个人感觉不怎么喜欢这种方式，我采用的是第二种，在ant的build.xml文件中装载：

{% highlight xml %}

<path id="maven-ant-tasks.classpath" path="${M2_REPO}/org/apache/maven/maven-ant-tasks/2.0.9/maven-ant-tasks-2.0.9.jar" />
<typedef resource="org/apache/maven/artifact/ant/antlib.xml" uri="urn:maven-artifact-ant" classpathref="maven-ant-tasks.classpath" />

{% endhighlight %}

这样就可以在后面使用maven-ant-tasks的task，比如artifact:pom，artifact:dependencies。<path />用于指定maven-ant-tasks-2.0.9.jar的路径，这里就有点麻烦了：maven-ant-tasks-2.0.9.jar这个东西放哪里好呢？直接放到项目中，然后存放到cvs/svn？显然不是一个好办法。考虑到maven本来就是干保存jar这行的，交给maven好了。maven-ant-tasks-2.0.9.jar本来就是一个依赖，可以在http://mvnrepository.com/找到：

<http://mvnrepository.com/artifact/org.apache.maven/maven-ant-tasks>

pom内容如下：

{% highlight xml %}

<dependency>
 <groupId>org.apache.maven</groupId>
 <artifactId>maven-ant-tasks</artifactId>
 <version>2.0.9</version>
</dependency> 

{% endhighlight %}

将这个内容随便找个空项目，加入后update一下dependency，在本地的maven repository中就会有它的jar文件。然后就可以直接使用它。

${M2_REPO}，这个是eclipse中通用的指向maven local repository的变量，大家使用maven命令建立eclipse项目时会遇到它。我们在这里可以直接使用这个变量来访问maven local repository。如果没有建立这个变量，请自行建立，上面注释中有详细说明。如果在eclispe之外比如命令行直接运行ant打包，则可以通过设置build.properties文件中的“M2_REPO=G:/soft/maven/localRepository”来指定。（这里用到ant的一个特性，属性一旦被赋值就不能修改，因此第一次赋值有效，在eclispe中运行，M2_REPO会使用eclispe中设置的值，如果eclispe没有设置或者命令行直接运行，M2_REPO属性会在build.properties文件装载时设置。）

装载ok后，接着是调用artifact:pom和artifact:dependencies 任务来指定pom.xml文件，再得到dependencies信息，后面的编译打包会使用到。注意useScope="runtime"，这个是为了最后打包时使用，只复制runtie时需要的jar包。

<target name="maven_info" />是用来打印maven相关的一些信息的，比如maven下的几个build目录。artifact:pom任务装载了整个pom，因此在后面可以访问到pom的信息，比如${maven.project.build.sourceDirectory}是java源代码目录，${maven.project.build.finalName}是最终的名称。

pom的内容可以参考这两个官方资料：

1） maven model

<http://maven.apache.org/ref/2.0.9/maven-model/maven.html>

2） Introduction to the POM

<http://maven.apache.org/guides/introduction/introduction-to-the-pom.html>

标准目录布局可以参考这个官方资料：

<http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html>

3.2 compile

这里是maven ant task的精髓所在，

{% highlight xml %}

<target name="compile" description="description" depends="init_maven">
     <javac srcdir="${maven.project.build.sourceDirectory}" destdir="${maven.project.build.outputDirectory}" classpathref="classpath.build" />
 </target>

{% endhighlight %}

结合之前maven初始化时的情况：

{% highlight xml %}

<artifact:pom id="maven.project" file="pom.xml" />
 <artifact:dependencies pathId="classpath.build" filesetid="maven.fileset.build">
     <pom refid="maven.project" />
 </artifact:dependencies>

{% endhighlight %}

可以看到，我们的项目完全遵循maven的标准做法，然后ant通过访问pom来得到相关的路径信息和classpath信息，完美结合。

target copyResource 完成了将resource copy到outputDirectory的任务，这里的resource都是classpath resource。

注： 这个有个问题，就是我没有找到通过pom得到resouce目录的方法，${maven.project.build.resources 只能得到pom文件中<resources/>, 对于里面包含的<resource>不知道该用什么方式来访问，如默认的：

{% highlight xml %}

<resources>
   <resource>
     <directory>src/main/resources</directory>
   </resource>
 </resources>

{% endhighlight %}

我试过用${maven.project.build.resources[0];${maven.project.build.resources(0), ${maven.project.build.resources.0,${maven.project.build.resources.resource
等都无效。找了很久都没有找到资料，只好作罢。直接写死src/main/resources吧。

3.3 jar

和compile类似，标准的ant jar，然后通过访问pom来得到相关的路径信息和打包文件名。注意这里的文件名和路径和maven的做法一致，也就是说和执行maven的compile命令结果是一样的。

{% highlight xml %}

<target name="jar" depends="compile, copyResource">
     <delete file="${maven.project.build.directory}/${maven.project.build.finalName}.jar"  failonerror="false" />
     <jar destfile="${maven.project.build.directory}/${maven.project.build.finalName}.jar" basedir="${maven.project.build.outputDirectory}">
     </jar>
 </target>

{% endhighlight %}

3.4 package， zip

package_prepare, copyLib, copyConfig, copyBin 这几个target都简单，体力活而已。zip也是。

    
4. 总结
    
上面的内容虽多，但是总结起来就只有两点：

1. maven ant task可以将maven的功能和ant的灵活性统一起来，对于非标准的打包情况非常适用

2. maven ant task的使用并不难，不过需要掌握不少maven的基本知识，比如pom，标准目录布局等。

另外，似乎，ant + Ivy会是更好的选择？