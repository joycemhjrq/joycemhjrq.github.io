---
layout: post
title: Redmine Windows下安装Redmine
categories: Redmine
tags: Redmine
---

[原文](http://www.cnblogs.com/afarmer/archive/2011/08/06/2129126.html)

## 一、Redmine安装。

### 1、准备

安装之前请阅读：[RailsInstaller简化了Rails在Windows上的安装过程](http://news.cnblogs.com/n/90182/)

英文原文：[RailsInstaller Provides Easy Rails on Windows Installation](http://www.infoq.com/news/2011/01/railsinstaller-windows)

[RailsInstaller](http://railsinstaller.org/)向Windows开发者提供了一种便捷的方式以轻松、快速创建Ruby on Rails 3应用。到目前为止，Windows开发者需要自己搭建好Ruby、RubyGems、Rails以及SQLite才能开始创建Rails应用。多亏了来自Engine Yard的Nic Williams博士及其团队，现在一切都变得简单异常。

目前的RailsInstaller提供了如下功能：

    通过向导的轻松安装
    Rails 3.0.3
    Ruby 1.8.7——p330
    SLQite 3.7.3
    Git 1.7.3.1
    DevKit

### 2、安装

注意：下面的操作要在联网的情况下进行。

转自：<http://www.cppblog.com/giigie/archive/2011/07/31/152160.html>

最近在一家公司实习。公司用的项目管理软件是开源的redmine，体验非常不错。我想这个东西不仅可以用来管理项目，也可以用来管理个人的学习工作进展啊。于是，我也在自己的虚拟机的2003 server 下安装。

之前为了安装wordpress，我已经在我的电脑上安装了 xampp。这是一个搭建服务器环境的傻瓜式安装包，里面集成了apache,php,mysql,phpMyAdmin,FileZilla FTP Server,Tomcat，这些组件可以选择安装。.

因为redmine是需要apache 和mysql的，所以正好。此外，redmine使用ruby写的，所以还需要ruby的环境。

配置ruby 的环境我选用的是railsinstaller 。

之前我在网上搜索安装redmine 的教程，配置ruby环境大多是用InstantRails。我实际用了一下，效果不太好。因为InstantRails从2007年开始就没有更新了，里面包含的很多组件版本都很旧，直接搭建的话需要升级各种组件，而且组件之间的依赖关系比较混乱，反正我是在里面绕了很多弯路。而且InstantRails 里面自带有很低版本的apache,mysql,phpMyAdmin,如果你电脑本来安装了这些，那么又要改这些服务的端口和对应的配置文件。总之一句话，InstantRails 已经过时了。

好了，现在开始进入正题吧。

1.去redmine的官方网站下载 redmine 的最新版本。我下载的的是<span style="color:red;font-weight:bold">redmine 2.5.1</span>然后弄清楚这个版本的需要的环境。Redmine 的网站的[Guide](http://www.redmine.org/projects/redmine/wiki/Guide) » [Installation Guide](http://www.redmine.org/projects/redmine/wiki/Installation_Guide) » 会清楚的告诉你这些事项的。

2.如果你的电脑上还没有apache ,php,mysql 建议你去下载xampp,如果有的话，跳过这步。

3.去railsinstaller 的官方网站去下载最新版的railsinstaller 。看它所包含的组件以及其版本。然后安装。

4.做好这些准备工作了。就可以开始配置redmine运行环境了。把redmine的解压到railsinstaller所产生的sites目录。在dos窗口进入sites\redmine目录下。

5.gem install rails -v=3.2.17         安装rails 3.2.17版本，这个redmine 2.5.1所要求的(可以根据提示安装正确的版本)。

<span style="color:red;font-weight:bold">（PS：如果安装版本不正确，或者安装多个版本，可以用如gem uninstall rails来卸载）</span>

6.gem install rack -v=1.1.1          安装rack 1.1.1版本，这个redmine 1.2.1所要求的。

7.gem install -v=0.4.2 i18n  这个根据安装时返回的错误命令提示来决定。比如的我就是提示Missing the i18n 0.4.2 gem. Please `gem install -v=0.4.2 i18n`

8.进入phpMyAdmin 创建数据库。执行以下三条sql 语句。

create database redmine character set utf8;

create user 'redmine'@'localhost' identified by 'my_password';

grant all privileges on redmine.* to 'redmine'@'localhost';

9.拷贝config/database.yml.example文件到config/database.yml，修改database.yml的内容 production:

      adapter: mysql2

      database: redmine

      host: localhost

      username: redmine

      password: my_password

Username 和password字段就是自己要设置的数据库的账号和密码。

10.回到dos 窗口下，设置Session Key，执行 rake generate_session_store

如果提示 mysql 错误，再执行如下语句 gem install mysql 

    1. 如果提示有其它没有安装成功的语句，而且bundle install 安装报rmagick错误的话，在Windows下安装这个一直安装不成功，所以可以用bundle install --without development test rmagick 

    2. 如果电脑已经有mysql了，安装adapter:mysql2 要跟mysql关联：gem install mysql2 -- --with-mysql-dir=F:/shanlin/xampp/mysql

    3. 替换libmysql.dll文件，由于RailsInstaller中的mysql编译版本问题，我们需要下载<http://dev.mysql.com/get/Downloads/Connector-C/mysql-connector-c-noinstall-6.0.2-win32.zip/from/pick>，将解压后的 lib\libmysql.dll 文件拷贝到 C:\RailsInstaller\Ruby1.9.3\bin下；

    3.3. 3中的办法经常会报 Incorrect MySQL client library version! This gem was compiled for 5.6.16 but the client library is 6.0.0等类似的错误，解决办法：把已经安装好的mysql/lib下的libmysql.dll,libmysql.lib文件拷贝到C:\RailsInstaller\Ruby1.9.3\bin下；
    
    4. rake generate_session_store 如果提示这句语句过时，用其它语句代替的话就用其它语句代替

11.然后set RAILS_ENV=production

rake db:migrate

rake redmine:load_default_data

执行rake load_default_data RAILS_ENV="production" 完后，会提示Select language: bg, bs, ca, cs, da, de, el, en, en-GB, es, eu, fi, fr, gl, he, hr, hu, id, it, ja, ko, lt, lv, mn, nl,no, pl, pt, pt-BR, ro, ru, sk, sl, sr, sr-CY, sv, th, tr, uk, vi, zh, zh-TW [en] zh

输入zh选择中文

12.最后ruby script/server webrick -e production  启动。<span style="color:red;font-weight:bold">如果报没有这个模块，就用：rails server 代替 </span>以后每次开机后都需要这句来启动。

13.打开浏览器 输入 http://localhost:3000 。账号，密码都是 admin 。

14.安装完了，就可以使用admin用户登录，密码也是admin，登入后，发现页面又变成了英文的，在这里要设置个人用户，修改语言为中文就可以了。然后进入管理页面，可以管理项目，用户，角色，权限，问题状态，跟踪类型，流程等。

<span style="color:red;font-weight:bold">如果打开页面，终端报错：WARN  Could not determine content-length of response body. Set content-length of the response or set Response#chunked = true

解决办法 ：

找到ruby的安装路径：

D:\Ruby193\lib\ruby\1.9.1\webrick\httpresponse.

Ruby代码 

    if chunked? || @header['content-length']   

修改为：

Ruby代码 

    if chunked? || @header['content-length'] || @status == 304 || @status == 204  

重启

</span>

## 兼容性提示

ruby1.9只能用adapter mysql2，不能用mysql