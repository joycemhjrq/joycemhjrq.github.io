---
layout: post
title: 重置mysql密码
categories: mysql
tags: mysql
---

- 重置mysql密码 <http://www.php100.com/html/webkaifa/database/Mysql/2011/0624/8359.html>

代码

	> mysqld stop
	> mysqld --skip-grant-tables --skip-networking

	> mysql
	> UPDATE mysql.user SET Password=PASSWORD('newpass') WHERE User='root' AND Host='localhost';
	> FLUSH PRIVILEGES;

	> mysqld restart


- 中文swift社区 <http://swift.sh/>

- 中文ruby社区 <https://ruby-china.org/>

- 中文python社区 <http://python-china.org/>