---
layout: post
title: loadrunner 性能指标定位系统瓶颈
categories: loadrunner
tags: analysis
---

## 判断CPU瓶颈

    1， %processor time 平均值大于95

    2， processor queue length大于2 (大于处理器个数+1).可以确定CPU瓶颈

    3， CPU空闲时间为零(zero percent idle CPU)

    4， 过高的用户占用CPU时间(%User Time)

    5,  过高的系统占用CPU时间(%Priviliaged Time：长期大于90%或者95%)

备注：

%User time(processor_total)表示耗费CPU的数据库操作，如排序，执行aggregate functions等。如果该值很高，可考虑增加索引，尽量使用简单的表联接，水平分割大表格等方法来降低该值

如果发现processor queue length显示的队列长度超过2,而处理器的利用率却一直很低,或许更应该去解决处理器阻塞问题,这里处理器一般不是瓶颈。

## 判断内存瓶颈与内存泄漏

    1，如果发生了内存泄漏,process\private bytes计数器和process\working set 计数器的值往往会升高,同时avaiable bytes的值会降低。

    2，如果Available Mbytes（剩余物理内存数）的值很小(4 MB 或更小)，则说明计算机上总的内存可能不足，或某程序没有释放内存。

## 定位磁盘瓶颈

    1， % Disk Time 和Avg.Disk Queue Length 的值 (应不大于组成物理磁盘的主轴数的1.5 到2倍) 很高，而Page Reads/sec页面读取操作速率很低，则可能存在磁盘瓶径。

    2，Physical Disk\ Disk Reads/sec and Disk Writes/sec 大于20 ms,则有可能磁盘瓶颈

    3，Avg.Disk sec/Transfer盘中写入数据的平均时间，单位是秒，一般来说，定义该值小于15ms最为优异，介于15-30ms之间为良好，30-60ms之间为可以接受，超过60ms则需要考虑更换硬盘或硬盘的RAID方式了

    4，Disk Transfers/sec 指在此盘上读取/写入操作速率。正常值<(Disk Bytes/sec)/3，此值过大表示系统要求的IO速度已接近硬盘的最大速度，要更换更快的硬盘。

备注：
如果使用 RAID 设备，% Disk Time 计数器会指示大于 100% 的值。

## 定位网络瓶颈

    Byte Total/sec 表示网络中接受和发送字节的速度，可以用该计数器来判断网络是否存在瓶颈（参考值：该计数器和网络带宽相除，<50%）
