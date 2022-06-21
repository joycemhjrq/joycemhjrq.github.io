---
layout: post
title: Cruisecontrol 常见问题及常见操作
categories: Cruisecontrol
tags: CC
---

## 常见问题

### connection refused to host:218.85.148.250

linux系统上CruiseControl运行force build的时候一直报：connection refused to host:218.85.148.250

网上各种办法都试过了，都不行，真是吐血呀，最后发现是因为：
（百度搜索：系统装了多个网卡吧，或者是虚拟机吧，者其实就是rmi的多IP紊乱问题，解决方法很简单，直接把另外一个网卡禁掉，或者是把虚拟机的网络连接给禁止就OK了！还有就是如果你安装的是新版本的red5的话，我是说0.8，那么你需要修改你的red5.project大概模样是这样，你找找吧，在conf这个文件夹下，修改里面的jmx将rmi.host=0.0.0.0改为你的服务器IP，就OK了）

按照上面的提示，在系统--》管理--》网络中，在设备选项卡中配置只留一个，其它的都不要勾选，然后重启下网络：service network restart后，再重启下CC就可以了，

## 常见操作

### cruisecontrol 发邮件

java ant发邮件：

<img src="/media/img/cruisecontrol-emial-1.jpg">

cruisecontrol发邮件（最后发现用第一种方法不是不发邮件，而是一直发到垃圾邮件里面了，汗。。。。）：
第一种：

<img src="/media/img/cruisecontrol-emial-2.jpg">

第二种：

<img src="/media/img/cruisecontrol-emial-3.jpg">

但是第一种，发的邮件只有一个链接，第二种发的邮件里包含的信息跟cruisecontrol上展示的差不多，所以建议使用每二种

### cruisecontrol bootstrappers

bootstrappers，设置每次schedule的build之前，该启动什么。 
例如填"projects/${project.name}/ABC/" />，即是到localWorkingCopy目录下,执行svn -update命令来更新代码。



