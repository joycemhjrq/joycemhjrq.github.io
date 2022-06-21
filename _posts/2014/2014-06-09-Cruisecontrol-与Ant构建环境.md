---
layout: post
title: Cruisecontrol 与Ant构建环境
categories: Cruisecontrol
tags: ant
---

1. 安装JDK1.6.0_10。并在系统路径里加上JAVA_HOME指向jdk的安装路径，在Path里加上%JAVA_HOME%"bin。

2. 下载CRUISECONTROL-BIN-2.7.3解压到指定目录。运行目录下的cruisecontrol.bat即可。

运行时如果在Starting HttpAdaptor时报错，可能是端口问题，可以修改bat里的“-jmxport 8000 -webport 8080 -rmiport 1099”的端口号。

    cruisecontrol.bat     启动文件;
    config.xml                 配置文件  (默认的配置文件名称);
    apache-ant-1.7.0    是CruiseControl自带的ant环境，CruiseControl要借助ant完成一些指定的任务;
    docs                          包括CruiseControl的使用文档和一个helloworld的使用范例;
    lib                              是运行需要的jar包;
    logs                          日志目录,构建时产生的log存储于此;
    project                      需要构建的项目所在目录，也是checkout默认的目录,所有的构建项目在project均以项目名为目录;在project目录下，有一个自带的项目connectfour，包含了src、lib、test和build.xml。
    webapps                web应用目录。CruiseControl内置了jetty作为web容器，可以通过web方式查看构建结果和手动创建项目;通过http://localhost:8080/
    artifacts                    输出目录，集成后生成的jar就保存在这里。

3. 配置

CruiseControl(以下简称 CC) 主要有两个配置文件：

一个是config.xml，是CC初始化、调度等任务参数的配置；

一个是build.xml，ant执行的配置文件，CC借助ant完成指定的任务，如checkout、compile、jar、test等。

学ant来这里http://ant.apache.org/manual/index.html

config.xml:

    <schedule interval="30">:间隔时间，单位秒。

build.xml:

    <project name="Hello world" default="compile">:最好指定一下default,这样直接运行ant就可以了。

    <target name="compile" depends="init" description="compile the source files">：运行complie前，要求运行init。会自动调用。

时间戳

    <target name="init" depends="" description="总控">
        <tstamp>
            <format property="BEGIN_TIME" pattern="MM/dd/yyyy hh:mm a" offset="-1" unit="minute" locale="en" />
        </tstamp>
        <echo>生成时间：${DSTAMP}-${TSTAMP}</echo>
        <echo>开始时间：${BEGIN_TIME}</echo>
    </target>

目录操作

    删除<delete dir="target/website" failonerror="no" />

    创建<mkdir dir="target/website/reporting/jsp" />

文件操作

    拷贝 <copy todir="target/zip" overwrite="true">
            <fileset dir=".">
                <include name="**/*.html" />
                <exclude name="**/target/**" />
                <date datetime="${BEGIN_TIME}" when="after" />
            </fileset>
        </copy>

    替换<replace dir="target/binzip" token="%%VERSION%%" value="${cc.release.label}">
            <include name="**/*" />
        </replace>

    正则替换<replaceregexp match=""r"n" replace="" flags="g" byline="false">
                <fileset dir="${war.dir}/site/layouts/" includes="**/*.jsp" />
            </replaceregexp>

    编译<javac srcdir="${src.java}" destdir="${basedir}/WebRoot/WEB-INF/classes" classpathref="classpath.build" debug="true">
            <compilerarg value="-Xlint:deprecation"/>
        </javac>
