---
layout: post
title: linux Ubuntu 安装 Postgresql Pgadmin
categories: linux
tags: ubuntu
---

## 第一步：在Ubuntu下安装Postgresql

sudo apt-get install postgresql

以上指令安装服务端和命令行客户端psql。

    /usr/lib/postgresql/8.4/              存放postgresql相关的二进制文件
    /usr/lib/postgresql/8.4/bin/        可执行文件
    /usr/lib/postgresql/8.4/lib/         共享库文件
    /etc/postgres/8.4/main/              存放postgresql配置文文件
    /var/lib/postgresql/                    postgres用户的主文件夹

## 第二步：修改PostgreSQL数据库的默认用户postgres的密码

PostgreSQL数据默认会创建一个postgres的帐号用户作为数据库的管理员，密码是随机的，所以：

首先，我们需要重置“postgres”用户的密码。

命令行如下：

sudo -u postgres psql(或者sudo su postgres -c psql)—>运行psql,psql是一个标准的postgressql客户端
postgres=# ALTER USER postgres WITH PASSWORD ‘postgres’;—>修改postgres的密码为postgres,不要忘记添加分号(回车后分号之前的sql语句才会立即执行)
postgres=# \q—>退出

修改了数据库中的密码之后，我们还需要修改linux下的用户“postgres”的密码：设定成与数据库中postgres帐号相同的密码，即postgres。

sudo passwd -d postgres———>删除密码

sudo -u postgres passwd(或者 sudo su postgres -c passwd)—–>创建密码

然后输入跟之前一样的密码。

现在，我们就可以在数据库服务器上用 postgres帐号通过psql或者pgAdmin等等客户端操作数据库了（暂时还不能远程访问）。

## 第三步：修改PostgresSQL数据库配置实现远程访问

首先，我们需要编辑postgresql.conf：

sudo gedit /etc/postgresql/8.4/main/postgresql.conf

现在，我们需要修改“连接和权 限”两行。

改变行：#listen_addresses = ‘localhost’ 修改为：listen_addresses = ‘*’

改变行：#password_encryption = on 修改为：password_encryption = on

保存并关闭gedit。

最后一步，我们必须设置谁才可以操作数据服务器，这一切都是在pg_hba.conf中完成的。

sudo gedit /etc/postgresql/8.4/main/pg_hba.conf

把以下内容添加到 pg_hba.conf底部：

    # to allow your client visiting postgresql server

host all all 0.0.0.0 0.0.0.0 md5

解释一下最后一行：

host表示允许的类型是主机；

第一个all是允许的数据库名字；

第二个all是允许的用户；

第一个0.0.0.0是允许访问的ip address；

第二个0.0.0.0是允许访问的subnet mask；

最后的md5表示密码的加密方式，如果将md5改成trust则可以让指定范围的主机数据库的时候不需要提供密码。

关于ip address和subnet mask，你也可以修改为你的机器IP地址(如10.13.19.53)和子网掩码(如 255.255.255.255)，这样就只有你自己的主机可以远程访问数据库了。

如果要使用一个IP地址范围，只需要把子网掩码设置成合适的值，如果子网掩码设置成0.0.0.0，则所有主机均可以访问数据库（IP可以任意设定），如 果将md5改成trust则可以让指定范围的主机访问指定的数据库的时候不需要提供密码。

重启服务器，以上的配置就生效了：sudo /etc/init.d/postgresql-8.4 restart

## 第四步：创建用户和数据库。

使用命令行创建用户和数据库：

sudo -u postgres createuser -D -P mynewuser—->-D该用户没有创建数据库的权利，-P提示输入密码，后面的选项都可以省略，命令执行的时候会提示用户选择yes或者no
 sudo -u postgres createdb -O mynewuser mydatabase—>-O设定所有者为mynewuser
或者使用psql创建用户和数据库：

 首先利用psql登录postgresql服务器:sudo -u postgres psql[ -U postgres -h 127.0.0.1]—->运行psql,后面[]中的内容可选，用于登录服务器，默认会登录本机；

 然后在psql程序中创建用户和数据库:用户名和数据库名称加上引号后才区分大小写，否则会自动转换成小写:

create user “mynewuser” with password ‘mynewuser nocreatedb;

create database “mydatabase” with owner=mynewuser;

## 第五步：如何安装和使用pgAdmin3客户端操作postgresql数据库

首先安装图形客户端pgAdmin3，安装命令行：sudo apt-get install pgadmin3
然后运行客户端并连接数据库，直接上图：

如果要让别人访问你的postgres:

在pg_hba.conf中(这个文件在pgAdmin的data目录下):

sudo gedit /etc/postgresql/8.4/main/pg_hba.conf

把以下内容添加到 pg_hba.conf底部：

    # to allow your client visiting postgresql server

host all all 0.0.0.0 0.0.0.0 md5
  

注意如果是Window7的话，要先把Window7的防火墙关掉，不然还是不能访问！！！

另：修改PgAdmin的密码：

文件-->更改密码
