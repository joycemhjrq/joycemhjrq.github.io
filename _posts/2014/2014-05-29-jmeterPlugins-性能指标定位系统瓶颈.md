---
layout: post
title: jmeterPlugins 性能指标定位系统瓶颈
categories: jmeterPlugins
tags: thread
---

## 一、线程组

### 1、jp@gc - Stepping Thread Group，如下图：

(sorry ，图片遗失，请自己想像)

类似loadrunner的场景设置，解释:
This Group will start 10 threads：这次的测试总共会起10个线程。
First , wait for 0 seconds：等待0s后开始起线程，也就是不等待直接起线程。
Then start 5 threads every 10 seconds：每起5个线程后会运行10s，再起余下的5个线程，再运行10s，以此类推。
Using ramp-up 2 seconds：前面每起多少个线程的时候花2s，与上面结合起来即2s内起5个线程，运行10s，然后再再2s内再起5个线程，再运行10s，以此类推。
Then hold load for 60 seconds. ：全部的线程起来后，运行60s 后开始停止（跟loadrunner类似，从jmeter聚合报告里面可以看出来，这里的hold load 的意思，其实是这些线程，一直在请求，相当于jmeter普通线程组里面的循环运行）。
Finally , stop 5 threads every 10 seconds：最后停止线程，5个线程停一次，等10s再停5个线程（当然，第一次是hold时间一过就先停5个线程，然后等10s再停余下的5个线程）。
从下面的图也可以很好的理解这个线程组的设置

### 2、jp@gc - Ultimate Thread Group，如下图：

(sorry ，图片遗失，请自己想像)

这个跟上面那个线程组有些类似，不过这个是几个设置的结合，像这里有设置两个线程组（1、不延迟，30s内起10个线程，hold 13s后，10s内停止； 2、不延迟，35s内起20个线程，hold 43s后，5s内停止），从下面的图可以看出，执行的时候，这两个线程组是同时按照自己的规则开始执行的，每一时刻，得到的结果都是两个线程组的叠加。