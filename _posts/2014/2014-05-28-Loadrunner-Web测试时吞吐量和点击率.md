---
layout: post
title: loadrunner Web测试时吞吐量和点击率
categories: loadrunner
tags: 
    - Throughtput
    - hits
---

## Web测试时吞吐量和点击率

首先先了解一下浏览器的工作原理，简单的说，当用户访问某个Html文件时，浏览器首先获得该Html文件，然后进行语法分析。如果这个Html文件包含图片、视频等信息，浏览器会再次访问后台的Web服务器，一次获取这些图像、视频文件，然后把Html和图像、视频文件组装起来，显示在屏幕上。

在LoadRunner的分析报告中，有Total Throughtput(bytes)和Total Hits两个重要的指标。我们简称为吞吐量和点击量。那么这两个指标的是怎么计算出来的呢？

例如页面<http://xxxx.com/default.aspx?keyword=新魔界>。如下图，用HttpWatch扫一下，

<img src="/media/img/loadrunner-hits-1.jpg">

每访问一次该页面，发生了一次“请求----接收”，发送了492 bites,接收了1593 bites,那么在一次访问中，吞吐量是1593 bites，还是492 bites+1593 bites呢？访问一次，点击量就是一次吗？

下面的实例就是用LR录制上面的访问行为，进行压力测试，看看分析报告中给出的数据到底是怎么计算的。

用LR录制脚本，选择HTTP协议。LR录制Web脚本有HTML模式和URL模式。用HTML模式录制是基于用户行为模拟，为每个用户请求生成单独的函数，而基于URL模式则与基于用户行为模拟不同的是不考虑任何用户操作，只考虑客户端发送的请求，注重于实际上系统做了什么。分别录制这两种模式的脚本：

1.例如页面http://xxx.com/default.aspx?keyword=新魔界

HTML模式录制的脚本如下图：

<img src="/media/img/loadrunner-hits-2.jpg">

URL模式录制的脚本如下图：

<img src="/media/img/loadrunner-hits-3.jpg">

从用HTML模式和URL模式录制的脚本来看，每访问一次该页面，是从客户端给服务器端发送了1次请求。

为了让性能测试的数据更加准确，在Run-time Settings里面设置Run Logic，让脚本跑100次，启动LR的Controller，把脚本方进去，只跑一个虚拟用户，跑一次。由于脚本里面设置了跑100遍，所以一次测试，LR将向后台发送请求100次启动压力测试，测试完成后打开Analysis分析结果，发现如下信息

运 行100次后总的吞吐量是159238 bites,总的点击量是100次，很显然，每次访问时的吞吐量是159238/100=1592.38≈1592bites,而每次访问时候的点击量是 100/100=1 次。用HTML模式的脚本和URL模式的脚本按照上面的场景执行，HTML模式下的 Total Throughput 是159238 bites ，URL模式下的Total Throughput 是159218，为什么运行100次后Total Throughput 会有20 bites的差别呢，这个还不是很清楚。

2.例如http://www.baidu.com/

HTML模式录制的脚本如下图：

<img src="/media/img/loadrunner-hits-5.jpg">

 URL模式录制的脚本如下图：

<img src="/media/img/loadrunner-hits-6.jpg">

观察上面录制的`http://www.baidu.com/`的脚本，URL模式的脚本中可以看到用户访问一次百度，实际上是从客户端给服务器端发送了5次请求。

还是和第一个例子一样，为了让性能测试的数据更加准确，在Run-time Settings里面设置Run Logic，让脚本跑100次，启动LR的Controller，把脚本方进去，只跑一个虚拟用户，跑一次。由于脚本里面设置了跑100遍，所以一次测试，LR将向后台发送请求100次。

启动压力测试，测试完成后打开Analysis分析结果，发现如下信息

运行100次后总的吞吐量是919400 bites，总的点击量是500次，很显然，每次访问时的吞吐量是919400/100=9194 bites, 很显然，每次访问时的点击量是500/100=5次

最后可以得到以下结论：

在Load Runner的Analysis中Total Throughput的含义是：在整个测试过程中，从服务器返回给客户端的所有bites数。Total Hits的计算不是按照用户的鼠标点击次数计算，而是按照客户端向服务器发送了多少次请求。例如在访问一次百度中，http://www.baidu.com/里面包含了2个gif、1个js、1个ico，用户只用鼠标点击一次就可以访问该页面，而Load Runner视该次访问的点击量是1+2+1+1=5次。



