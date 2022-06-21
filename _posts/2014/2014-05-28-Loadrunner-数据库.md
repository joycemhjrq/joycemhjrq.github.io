---
layout: post
title: loadrunner 数据库
categories: loadrunner
tags: oracle
---

## loadrunner 连接数据库第一步：安装oracle客户端

在lr连接oracle数据库时报错：未找到Oracle客户端和网络组件。这些组件是由Oracle公司提供的，是 Oracle8i版(或更高)客户软件安装的一部分。在安装这些组件之前，将无法使用此提供程序。

解决办法：安装oracle客户端：

<img src="/media/img/loadrunner-oracle-install-1.jpg">

-->下一步

<img src="/media/img/loadrunner-oracle-install-2.jpg">

-->管理员

<img src="/media/img/loadrunner-oracle-install-3.jpg">

-->选择安装路径，下一步，（检查通过），下一步，安装

<img src="/media/img/loadrunner-oracle-install-4.jpg">

-->下一步

<img src="/media/img/loadrunner-oracle-install-5.jpg">

-->下一步

<img src="/media/img/loadrunner-oracle-install-6.jpg">

-->下一步（这里的服务名是数据库的服务名，它定义在：oracle\product\10.2.0\client\network\admin\下的tnsname....bak文件中service_name,这里如测试环境的服务名：gh）
-->选择：“TCP”协议

<img src="/media/img/loadrunner-oracle-install-7.jpg">

-->下一步（进行测试）

<img src="/media/img/loadrunner-oracle-install-8.jpg">

-->（测试失败）更改登录

<img src="/media/img/loadrunner-oracle-install-9.jpg">

-->确定

<img src="/media/img/loadrunner-oracle-install-10.jpg">

-->下一步

-->取一个网络服务名

-->根据需要看是否需要配置另一个Net服务名。。。。。

之后如果需要配置新的Net服务的话，可以打开：开始菜单-->oralce-->配置和移植工具-->Net Configuration Assistant（net配置助手）进行配置

也可以打开：开始菜单-->oralce-->配置和移植工具-->Microsoft ODBC 管理员 添加你需要的东西

## loadrunner 连接数据库

<img src="/media/img/loadrunner-oracle-1.jpg">

这里的Oracle ODBC Driver是上面安装的oracle客户端

<img src="/media/img/loadrunner-oracle-2.jpg">

这里的Data Source Name是数据库的sid如mswdb，但是这里的Data Source 下的Service Name不是数据库的sid(如mswdb)而是你的oracle客户端配置的如下：

<img src="/media/img/loadrunner-oracle-3.jpg">

<img src="/media/img/loadrunner-oracle-4.jpg">

如，我要连接202这台的数据库，那前面的lr的设置里面Data Source下的Service Name我就要写mswdb202,以此类推。。。

## loadrunner 数据库取数据

利用lr的Data Wizard进行数据库连接取数据的时候，最好是先参数化一个参数，把所要的职取出来，然后再参数化相应的参数，取数据库里面取出来的数据的第几列，如果先把所有的参数参数化后，再从数据库里面取，很容易报：共享违例，暂时没有查出来原因，也不想查，先这么解决吧。

## loadrunner 数据库参数化

loadrunner可以参数化一些参数，其中一种可以用直接连接数据库取值的方式：
选中参数，右键：Replace with Paramater,选择type，点击Properties：

<img src="/media/img/loadrunner-data-1.jpg">

点Data Wizard后可以设置数据库：

<img src="/media/img/loadrunner-data-2.jpg">

下一步后，点Create-->机器数据源-->新建-->系统数据源-->下一步：

1、postgres数据库：

选择你需要的数据源（如：PostgresSQL ODBC Driver(UNICODE)）-->下一步-->完成：

<img src="/media/img/loadrunner-data-3.jpg">

这时可以点击Test查看你的数据库配置是否正确
这些做完后，输入sql语句，Finish即可：

<img src="/media/img/loadrunner-data-4.jpg">

2、oralce数据库：

先安装oracle客户端，其间有建立Net服务名

（前面跟postgres数据库一样，然后）选择你安装的oracle：

<img src="/media/img/loadrunner-data-5.jpg">

-->下一步-->完成

<img src="/media/img/loadrunner-data-6.jpg">

-->Data Source Name：the name used to identify the data source to ODBC. For example, "odbc-pc". You must enter a Data Source Name.

Description - a description or comment about the data in the data source. For example, "Hire date, salary history, and current review of all employees." The Description field is optional.

TNS Service Name - the location of the Oracle database from which the ODBC driver will retrieve data. This is the same name entered in configuring network database services using the Oracle Net Manager. For more information, see the Oracle Net Services documentation and Using the Oracle ODBC Driver for the First Time. The TNS Service Name can be selected from a pulldown list of available TNS names. For example, "ODBC-PC". You must enter a TNS Service Name.
User ID - the user name of the account on the server used to access the data. For example, "scott". The User ID field is optional.


-->点击：Test Connection看是否可以连接

<img src="/media/img/loadrunner-data-7.jpg">

-->输入用户名密码-->OK-->OK

<img src="/media/img/loadrunner-data-8.jpg">

-->选择你的数据库源名称，然后确定

-->输入用户名密码

<img src="/media/img/loadrunner-data-9.jpg">

在SQL statement框里输入你要查询的sql语句：Finish就OK了