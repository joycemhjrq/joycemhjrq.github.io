---
layout: post
title: linux 创建oracle用户表空间
categories: linux
tags: oracle
---

就是在已有的数据库实例上创建一个新的帐号，访问一些新的表

## 操作步骤如下：

    1、登录linux，以oracle用户登录（如果是root用户登录的，登录后用 su - oracle命令切换成oracle用户）
    2、以sysdba方式来打开sqlplus，命令如下：sqlplus "/as sysdba"
    3、查看我们常规将用户表空间放置位置：执行如下sql：
        select name from v$datafile;
    上边的sql一般就将你的用户表空间文件位置查出来了。
    以特定用户和数据库访问是：sqlplus username/password@数据库名称 (比如：sqlplus system/123456@casdb)
    如果是进来到SQL>这里，可以用conn username/password@casdb;
    4、创建用户表空间：
        CREATE TABLESPACE NOTIFYDB DATAFILE '/oracle/oradata/test/notifydb.dbf' SIZE 200M AUTOEXTEND ON EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
    删除表空间：
        drop tablespace mytbs01;
    5、创建用户，指定密码和上边创建的用户表空间
        CREATE USER hc_notify IDENTIFIED BY hc_password DEFAULT TABLESPACE NOTIFYDB;
    特别注意：每个sql语句后面都要加上分号; 不然点回车是出现换行，会2,3,4,5 行这样。
    6、赋予权限
        grant connect,resource to hc_notify;
        grant unlimited tablespace to hc_notify;
        grant create database link to hc_notify;
        grant select any sequence,create materialized view to hc_notify;
    7 、 取消已经授予的权限
        revoke connect, resource from hc_notify;
    8 . 删除已经创建的用户
        DROP USER 用户名 CASCADE;
    经过以上操作，我们就可以使用hc_notify/hc_password登录指定的实例，创建我们自己的表了
    然后可以启动控制台 quit 后  isqlplus start
    访问：http://127.0.0.1:5560/isqlplus. 用上面的账号密码 登陆。就可以创建自己的表了。

当用$ORACLE_HOME/bin 下的  ./dbca 创建自己的数据库时。默认系统用有 system,  sys 等。可以在

终端命令行输入 sqlplus system/123456@sso  (说明 system 系统用户，123456是它的密码，在创建数据库时有叫填写， sso是创建的数据库，也即实例 sid)

可以连接到SQL>

这时为自己的数据库创建自己的用户。

SQL>Create user test identified by test;   （即用户名：test 密码也：test）

SQL>Grant all privileges to test;（这里简单的把所有权限都赋予了east）

这样自己的数据库和连接用户就创建成功了。

连接url：jdbc:oracle:thin:@localhost:1521:sso

用户名：tset  密码：test

OK了。哈哈！！

## 实践：

    创建用户：Create user test identified by test default tablesapce casdb/mswdb/paymentdb;

    赋予权限：grant connect,resource to test;

    创建视图权限：grant create any view to test;
