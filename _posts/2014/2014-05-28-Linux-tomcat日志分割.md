---
layout: post
title: linux tomcat日志分割
categories: linux
tags: tomcat
---

最近由于工作需要，tomcat 的catalina.out文件的不断扩大，导致系统磁盘空间边变小，而且管理也难于管理，所以想用一种工具来分割它。网上找了找用cronolog

## 安装过程如下：

### 1.下载（最新版本）

    #  wget http://cronolog.org/download/cronolog-1.6.2.tar.gz

### 2、解压缩

    # tar zxvf cronolog-1.6.2.tar.gz

### 3、进入cronolog安装文件所在目录

    # cd cronolog-1.6.2

### 4、运行安装

    #chmod 777 configure  (改变可执行文件权限)

    # ./configure

    # make

    # make install

### 5、查看cronolog安装后所在目录（验证安装是否成功）

    # which cronolog

一般情况下显示为：/usr/local/sbin/cronolog

要想分割tomcat的catalina.out，需作如下工作：修改tomcat bin目录下的catalina.sh文件中的`org.apache.catalina.startup.Bootstrap “$@” start \>> “$CATALINA_BASE”/logs/catalina.out 2>&1 &` 为 `org.apache.catalina.startup.Bootstrap "$@" start 2>&1 \ | /usr/local/sbin/cronolog "$CATALINA_BASE"/logs/catalina.%Y-%m-%d.out >> /dev/null &`

同时，文件中有一行

touch “$CATALINA_BASE”/logs/catalina.out

可以注释掉，完成之后重起Tomcat就可以了，

看logs文件中是否有catalina.2009-07-01.out样式的日志。

## 遇到的问题：

### 1、configure:error:no acceptable cc found in $path ，解决办法：

这是缺少GCC编译器造成的，安装即可，网上也有说是路径问题

在终端中输入命令#yum install gcc

安装过程中如果报下载包失败，报：error downloading packages

可以yum clean all,清空后重新下载，然后重新再安装 yum install gcc

如果遇到Error Downloading Packages:原因：操作系统原理没有默认安装ftp工具，安装ftp:

yum install ftp

### 2、没有make命令：

yum install make （不用下载，直接install,linux自带的，如果不行，可以去下载然后运行）(make下载地址：http://ftp.gnu.org/pub/gnu/make/)
