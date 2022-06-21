---
layout: post
title: loadrunner Controller
categories: loadrunner
tags: Controller
---

## Duration

例：Run for 00:05:00(HH:MM:SS)

持续时间设置交覆盖Vuser迭代设置。这意味着，如果将持续时间设为5分钟，那么Vuser将继续在5分钟时间内运行尽可能多的迭代，即使运行时设置仅指定一次迭代。

### 设置

在场景设置duration选项(Duration:run for 00:02:00)

不知道是duration是表示每个用户只运行2分钟，还是所有用户登陆成功后，再一起运行2分钟。

脚本介绍：vuser_init，vuser_end二部分都为空，action部分的脚本打开几个网页。

经过四次的测试，Duration选项一直为run for 00:02:00，每次更换Start Vuser的设置，

结果显示：

duration：run for 00:02:00是所有用户登陆成功后，再一起运行2分钟。通过不同设置，会导致点击率和平均事务时间有变化，设置场景时要慎重考虑。 

## 系统资源的监控几个重要计数器

如在Windows Resources里面右键：Add Measurement....

添加如下计数器：

内存Memory相关：

	1、Available MBytes
	2、Page/sec ; Page Faults/sec ; Pages Input/sec ; Page Reads/sec ; Transition ; Faults/sec
	3、Cache Bytes
	4、File Cache Hits% ; File Cache Flushes ; File Cache Hits
	5、Pool Paged Bytes Pool Nonpaged Bytes
	6、Virtual Bytes (实例inetinfo、dllhost) Working Set(实例inetinfo、dllhost) Dllhost#n 进程都要添加计数器
	7、Privatae Bytes
	8、Committed Bytes

<img src="/media/img/loadrunner-memory-1.jpg">

<img src="/media/img/loadrunner-memory-2.jpg">

<img src="/media/img/loadrunner-memory-3.jpg">

Processor相关：

	1、Processor Queue Length
	2、%Processor Time
	3、Context Switches/sec
	4、%Privileged Time
	5、Context Switches/sec(实例化inetinfo和dllhost进程)
	6、Interupts/sec  %DPC Time

<img src="/media/img/loadrunner-process-1.jpg">

<img src="/media/img/loadrunner-process-2.jpg">

<img src="/media/img/loadrunner-process-3.jpg">

网络吞吐量以带宽：

	1、Bytes Total/sec
	2、Maxinum Connections、Total Connection Attempts

<img src="/media/img/loadrunner-network.jpg">

磁盘相关：

	1、%Processor Time ；%Privileged Time
	2、%Disk Time
	3、Average Disk Queue Length
	4、Average Disk Read Queue Length
	5、Average Disk Write Queue Length
	6、Average Disk sec/Read
	7、Average Disk/sec/Transfer
	8、Disk Readks/sec
	9、Disk Writes/sec

<img src="/media/img/loadrunner-cipan-1.jpg">

<img src="/media/img/loadrunner-cipan-2.jpg">

Web 应用程序

	1、Request/Sec  Request Executing
	2、Request Wait Time ；Request Executing Time ; Request Queued

<img src="/media/img/loadrunner-web.jpg">

IIS 5.0相关：

	1、Bytes Sent/Sec
	2、Bytes Received/Sec
	3、Get Requet/Sec
	4、Rost Request/Sec

<img src="/media/img/loadrunner-IIS5.jpg">

SQL Server相关：

	1、%Processor Time
	2、Logins/sec
	3、Cache His Ratio(all instances)
	4、User Connections
	5、Lock Waits/sec
	6、Buffer Cache Hit Ratio
	7、Batch Requests/sec
	8、Lazy Writes/sec
	9、Page Reads/sec
	10、Transactions/sec

<img src="/media/img/loadrunner-sqlserver-1.jpg">

<img src="/media/img/loadrunner-sqlserver-2.jpg">

## 输出窗口

脚本中用：lr_output_message(lr_eval_string("username用户名 now is {username}"));

Run-Time Setting中将log的Enable logging勾选去掉。

然后在Controller中运行完成后，点View-->Show Output后可以查看到lr_output_message打印出来的内容。 
