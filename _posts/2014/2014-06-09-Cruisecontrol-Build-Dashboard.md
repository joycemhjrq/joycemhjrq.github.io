---
layout: post
title: Cruisecontrol Build Dashboard
categories: Cruisecontrol
tags: dashboard
---

## 一 CruiseControl Dashborad是什么

CruiseControl Dashboard是一个强大的工具帮助你可视化你的projects的状态。先前projects的build结果被彩色标记显示，以至于你能立即地获 得你的projects的build结果。builds结果被用来表现当前project的状态的图标来更直接地表现（例如，暂停，队 列，building）。

<img src="/media/img/cruisecontrol-dashboard-1.jpg">

当把你的鼠标放到用来表示project builds的彩色方块上，你能够看到关于project跟多的信息，例如project的名字，project运行的server和最后一个build发生的时间。

彩色方块的意义：

通常，方块有绿色或红色的背景色，红色表示最后一个build失败，绿色表示最后一个build成功。build保持它的状态越长，它的颜色就越 深。特殊情况是如果一个project上次build成功，当前build时显示为绿色，如果一个project上次build失败，当前build时显 示为橙色。

下面是一些你可能看到的状态的实例，且存在更多的组合情况，但是你应该能够从下面的例子推断他们，

<img src="/media/img/cruisecontrol-icon-1.jpg"> 表示一个project的最后的成功的build超过了24小时，且当前正在队列等待build；

<img src="/media/img/cruisecontrol-icon-2.jpg"> 表示一个project在过去的24内的最后的build是成功的；

<img src="/media/img/cruisecontrol-icon-3.jpg"> 表示一个project的build失败且超过了24小时；

<img src="/media/img/cruisecontrol-icon-4.jpg"> 表示一个project在过去的24内的最后的build是失败的，且当前处于暂停状态；

<img src="/media/img/cruisecontrol-icon-5.jpg"> 表示一个project的上次build是成功的，且当前正处于build状态；

<img src="/media/img/cruisecontrol-icon-6.jpg"> 表示一个project处于非激活或停止状态；

下面是你能够执行的动作的列表：访问project build的RSS feed，使用CCTray或CruiseControl JMX console访问xml feed.

<img src="/media/img/cruisecontrol-dashboard-2.jpg">

## 二 Dashborad的Build 页面

<img src="/media/img/cruisecontrol-dashboard-3.jpg">

这就是build页面，它能使你查看每个project build和build的状态，且能够对project执行下列的动作：

<img src="/media/img/cruisecontrol-icon-7.jpg"> 查看所有成功的build；

<img src="/media/img/cruisecontrol-icon-8.jpg"> 查看所有的build；

<img src="/media/img/cruisecontrol-icon-9.jpg"> 强制启动一个build；

<img src="/media/img/cruisecontrol-icon-10.jpg"> 访问每个project的build RSS feed和CruiseControl JMX console；

三 Dashborad的Build 详细页面

1）build详细页面

<img src="/media/img/cruisecontrol-dashboard-4.jpg">

build详细页面包含了project的build信息，例如最后成功的build，build number，build的间隔和最后25个build。

2）当前build的详细页面

<img src="/media/img/cruisecontrol-dashboard-5.jpg">

这个是当前build的project的详细页面。build的输出会被实时地显示在这里。

## 四 Dashborad的administration页面

<img src="/media/img/cruisecontrol-dashboard-6.jpg">

在这个页面你能够看到dashboard配置的详细信息，例如是哪个build，正在运行的JVM的os和JVM版本信息。通过单击 configuration子tab，可以看到dashboard从哪里读取configuration和configuration的具体信息。

## 五 Dashboard的限制

* 如果你移除了project的log文件，Dashborad不能自动地发现且标志project为非激活。你需要refresh页面。如果你删除了project的log文件夹，CruiseControl不会重新创建它知道你重新启动CruiseControl。

* 如果你移除了project从CruiseControl的config.xml配置文件，Dashborad不能自动地发现且标志project为中止。你需要refresh页面。

* 尽管build loop能够自动应用对config.xml配置文件的修改，但是Dashborad不能获得dashborad-config.xml配置文件的修改。你需要重新启动Dashborad来使修改生效。

## 六 Dashborad的配置

有以下三种方法配置Dashborad，如下：

* J2EE server的web.xml。这种方法是不赞成的；

*Dashborad-config.xml 这种是配置Dashborad推荐的方法。默认的dashbord-config.xml随CruiseControl一起发布且与用来配置build loop的config.xml位于相同的位置。

*系统属性（system properties） 你可以使用系统属性来重写配置文件的设置；

系统属性：

管理员可以传递系统属性通过cruisecontrol.sh/cruisecontrol.bat或者web container启动脚本.

<table cellspacing="0" cellpadding="0" border="2">
<thead>
<tr>
<th>Property</th>
<th>Value</th>
<th>Default Value</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>cc.config.forcebuild</td>
<td>enabled/disabled</td>
<td>enabled</td>
<td>是否用户能够从gui来强制启动build</td>
</tr>
<tr>
<td>cc.logdir</td>
<td>String</td>
<td>logs</td>
<td>用来指定CruiseControl的log的全目录</td>
</tr>
<tr>
<td>cc.artifacts</td>
<td>String</td>
<td>artifacts</td>
<td style="height: 18px;">用来指定build的binary/master的全路径</td>
</tr>
<tr>
<td>dashboard.config</td>
<td>String</td>
<td>dashboard-config.xml</td>
<td style="width: 623px; height: 23px;">用来指定Dashborad配置文件的路径</td>
</tr>
</tbody>
</table>

Dashborad配置文件Dashborad-config.xml：　　

<buildloop> tag用来指定cruisecontrol build loop相关的属性logsdir和artifactsdir。

<features> tag 包含了自定义Dashborad的属性allforcebuild。

<trackingtool> tag 包含属性projectname,baseurl和keywords，用来创建project管理工具的提交注解的链接。你的projects可以有零个 或一个<trackingtool> tag。Dashborad将解析提交消息来发现指定关键字后面的story number，然后用指定的baseurl和story number来产生链接。例如如果你提交source code且提交消息为 "I fixed bug #425" 且定义<trackingtool>像 <trackingtool projectname="cc" baseurl="http://mingle05/projects/ccee/cards/" keywords="#,build"/>，那么Dashborad会为你的提交消息产生链接 "<a href=http://mingle05/projects/ccee/cards/425>I fixed bug #425</a>"  。

<subtabs> tag 被用来为build 详细页面指定widget plugin，你可以有零个或一个subtabs tags，其中一个subtabs可以包含多个subtab tags。 