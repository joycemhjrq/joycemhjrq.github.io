---
layout: post
title: Cruisecontrol binary安装和启动
categories: Cruisecontrol
tags: binary
---

## 一CruiseControl安装

0）安装Java SDK，然后设置JAVA_HOME环境变量。

1）下载解压，例如2.8.3版本：http://sourceforge.net/projects/cruisecontrol/files/CruiseControl/2.8.3/。

2）使用cruisecontrol.bat 或 cruisecontrol.sh 来启动CruiseControl。

3）在http://localhost:8080/cruisecontrol/ 查看project的log结果。

4）在 http://localhost:8080/dashboard 查看所有的project的report结果。

5）在 http://localhost:8080/documentation可以查看本地版本的帮助文档。

6）在 http://localhost:8000/ 查看CruiseControl JMX console （Java Management Extensions， 用来管理和监视CruiseControl运行）。

 

## 二 CruiseControl安装后文件

1）如图

<img src="/media/img/cruisecontrol-install.jpg">

2）解释

*ant，对ant script进行支持；

*etc/lib，包含jetty，jetty是免费的，用来提供web server和javax.servlet容器，而且支持Web Sockets, OSGi, JMX, JNDI, JASPI, AJP 和其他一些集成；

*webapps，包含了build result JSP和dashborad等；

*log/projects/artifacts,是使用cruisecontrol进行build时的log，source code，binary/master等目录；

*Cruisecontrol.bat/cruisecontrol.sh是用来启动cruisecontrol的脚本；

*config.xml/dashboard-config.xml包含了所有的cruisecontrol运行所需要的配置；

## 三 CruiseControl启动

    cruisecontrol.bat
    REM Set this if you're using SSH-based CVS
    REM set CVS_RSH=

    REM Uncomment the following line if you have OutOfMemoryError errors
    REM set CC_OPTS=-Xms128m -Xmx256m

    REM The root of the CruiseControl directory.  The key requirement is that this is the parent
    REM directory of CruiseControl's lib and dist directories.
    REM By default assume they are using the batch file from the local directory.
    REM Acknowledgments to Ant Project for this batch file incantation
    REM %~dp0 is name of current script under NT
    set CCDIR=%~dp0

    :checkJava
    if not defined JAVA_HOME goto noJavaHome
    set JAVA_PATH="%JAVA_HOME%\bin\java"
    set CRUISE_PATH=%JAVA_HOME%\lib\tools.jar
    goto setCruise

    :noJavaHome
    echo WARNING: You have not set the JAVA_HOME environment variable. Any tasks relying on the tools.jar file (such as "<javac>") will not work properly.
    set JAVA_PATH=java

    :setCruise
    set LIBDIR=�DIR%lib

    set LAUNCHER=%LIBDIR%\cruisecontrol-launcher.jar

    set JETTY_LOGS=�IDR%logs

    set EXEC=%JAVA_PATH% �_OPTS% -Djavax.management.builder.initial=mx4j.server.MX4JMBeanServerBuilder "-Djetty.logs=%JETTY_LOGS%" -jar "%LAUNCHER%" %*
    -jmxport 8000 -webport 8080 -rmiport 1099
    echo %EXEC%
    %EXEC%

1）%~dp0 为cruisecontrol.bat的目录。也即cruisecontrol解压以后的根目录。

2）必须安装JavaSDK和定义JAVA_HOME。

 

## 四 CruiseControl的手动启动

通过执行jar也可以启动CruiseControl，命令如下：java -jar lib/cruisecontrol-launcher.jar

如果你指定-jmxport和-rmiport或者其中之一，JMX server将会被启动，可以被用来控制CruiseControl（强制启动project builds，监视project的状态，改变log level，等等）。

如果你指定了-webport参数，web server将会被启动来运行CruiseControl reporting程序。

注意所有的参数都是可选的，如果你只是想使用config.xml来在当前工作目录启动CruiseControl，且不想运行JMX来控制CruiseControl，你不需要设置任何参数。

大部分用户指定-jmxport参数，因为JMX提供的html接口更方便。

## 五 CruiseControl启动选项

1）标准选项

    -configfile filename  相对路径来指定CruiseControl的配置文件，默认为"config.xml".
    -dashboardurl url  指定dashboard的home page的地址。默认为http://localhost:8080/dashboard.
    -postinterval [number]  指定build loop发布builds information到dashboard的间隔时间，单位为秒。默认为5秒。
    -postenabled [true|false]  指定是否要build loop发布builds information到dashboard。默认为是。
    -debug  改变CruiseControl内部的log4j的等级为debug。注意这里的修改不会导致你的ANT script的log等级为ant -debug，如果你需要请在config.xml中指定。 
    -help or -?  打印帮助信息。
    -log4jconfig url  指定log4j的config的url。

2）web相关选项 （jetty的具体的设置，可以通过 /cruisecontrol-bin-2.8.3/etc目录下的jetty.xml来设置）

    -jettyxml filename   Jetty配置文件。默认为jetty.xml。  
    -webport port  Jetty的port number。Jetty不会启动除非指定此选项或-webapppath。默认地设置为8080。
    -webapppath path  cruisecontrol.war文件的路径. Defaults to ./webapps/cruisecontrol.
    -ccname name  与此CruiseControl关联的一个逻辑名字。此名字将被显示在reporting application的status page。  

3）JMX相关选项

    -agentutil [true|false]   指定是否load JMX build agent utility。默认为true。
    -jmxport [port number]  指定JMX http adapter的port number。这将激活CruiseControl JMX的admin功能。如果不指定默认为8000，你可以通过http://localhost:8000/来访问jmx，或者通过Control panel jsp tab 或 jsp reporting application来访问。
    -rmiport [port number]  指定jmx rim adapter 和 connector server的port number。这将激活CruiseControl JMX的admin功能。如果不指定默认为1099，如果没有rmi registry运行在指定的port，则你的registry将被启动。
    -xslpath directory  指定JMX http adapter使用的自定义XSLT文件的位置。正常地，CruiseControl使用安装默认的。
    -user  user id  指定JMX http adapter的user。指定后必须使用此用户来使用JMX web interface。
    -password password  指定JMX的user的password。  
