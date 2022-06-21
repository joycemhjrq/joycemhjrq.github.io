---
layout: post
title: jmeterPlugins 监控器
categories: jmeterPlugins
tags: monitor
---

## 1、jp@gc - Actiive Threads Over Time：

每秒的活动线程数， X轴表示访问的时刻，Y轴表示活动线程数，F(X,Y)表示某个时刻的活动线程数

## 2、jp@gc - AutoStop Listener ：自动停止监听器

average Response Time is greater than 10000ms for 10 seconds ：连续10s平均响应时间大于10000ms就停止测试。

average Latency is greater than 5000ms for 10 seconds ：连接10s平均等待时间大于5000ms就停止测试。

Error Rate is greater than 50% for 10 seconds ：10s内错误率一直高于50％就停止测试。

## 3、jp@gc - Bytes Throughput Over Time:

不同时间吞吐量展示（图表）（聚合报告里，Throughput是按请求个数来展示的，比如说1.9/sec，就是每s发送1.9个请求； 而这里的展示是按字节Bytes来展示的图表）

## 4、jp@gc - Composite Graph： 混合图表

在它的Graphs里面可以设置多少个图表一起展示，它可以同时展示多个图表

## 5、jp@gc - Console Status Logger：一些log的设置。

This is a simple listener that prints short summary log to console while JMeter is running in non-GUI mode. It also writes the same info into jmeter.log in GUI mode.

Note that response time and latency values printed are averages.

## 6、jp@gc - Flexible File Writer：这个插件允许你灵活记录测试结果

Filename：结果记录的地方

Overwirte existing file：是否覆盖这个文件

Ｗrite File Header：文件的头(即文件的第一行)

Record each sample：如何记录不同的sample（记录哪些内容，用什么顺序，如何隔开不同的值）

Write File Footer：文件的结尾（即文件的最后一行）

<br>
<table>
<tbody>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">startTime</td>
<td style="border: 1px solid #ccc; padding: 5px;">epoch time when
request was started</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">endTime</td>
<td style="border: 1px solid #ccc; padding: 5px;">epoch time when
reading response ended</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">responseTime</td>
<td style="border: 1px solid #ccc; padding: 5px;">response time,
time to full response loaded</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">latency</td>
<td style="border: 1px solid #ccc; padding: 5px;">latency, time to
first response byte received (if available)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">responseCode</td>
<td style="border: 1px solid #ccc; padding: 5px;">response code
(200, 404 etc.)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
responseMessage</td>
<td style="border: 1px solid #ccc; padding: 5px;">response message
(OK, Not Found etc.)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
responseHeaders</td>
<td style="border: 1px solid #ccc; padding: 5px;">response headers
(if present in sample)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">responseData</td>
<td style="border: 1px solid #ccc; padding: 5px;">response
data</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">requestData</td>
<td style="border: 1px solid #ccc; padding: 5px;">request data from
sample</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">sentBytes</td>
<td style="border: 1px solid #ccc; padding: 5px;">number of request
bytes sent, if available</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
receivedBytes</td>
<td style="border: 1px solid #ccc; padding: 5px;">number of request
bytes sent (if available)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">threadName</td>
<td style="border: 1px solid #ccc; padding: 5px;">name of thread in
Thread Group that processed request</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">sampleLabel</td>
<td style="border: 1px solid #ccc; padding: 5px;">name of the
sampler that made request</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
isSuccsessful</td>
<td style="border: 1px solid #ccc; padding: 5px;">is response was
marked as successful</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">isFailed</td>
<td style="border: 1px solid #ccc; padding: 5px;">is response was
marked as failed (surrogate field)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
startTimeMillis</td>
<td style="border: 1px solid #ccc; padding: 5px;">same as
startTime, but divided by 1000 (surrogate field, example:
1311121131.362)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
endTimeMillis</td>
<td style="border: 1px solid #ccc; padding: 5px;">same as endTime,
but divided by 1000 (surrogate field)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
responseTimeMicros</td>
<td style="border: 1px solid #ccc; padding: 5px;">same as
responseTime, but multiplied by 1000 (surrogate field)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
latencyMicros</td>
<td style="border: 1px solid #ccc; padding: 5px;">same as latency,
but multiplied by 1000 (surrogate field)</td>
</tr>
<tr>
<td style="border: 1px solid #ccc; padding: 5px;">
<tt>variable#&lt;N&gt;</tt></td>
<td style="border: 1px solid #ccc; padding: 5px;">Sample variable
with index N, see below for details</td>
</tr>
</tbody>
</table>

## 7、jp@gc - Hits per Second：每秒点击量

## 8、jp@gc - Loadosophia.org Uploader：

This is special plugin in for uploading results to Loadosophia.org immediately after test end.

## 9、jp@gc - Page Data Extractor：

以图表的形式展示页面上你想得到的一些结果，它以正则表达式的形式获取key和vale。（下面的ｋｅｙ和ｖａｌｕｅ的正则表达式以括号标志）

## 10、jp@gc - PerfMon Metrics Collector：

服务器性能监测控件，包括ｃｐｕ,ｍｅｍｏｒｙ,ｎｅｔｗｏｒｋ　Ｉ/Ｏ等等。（JMeter cannot retrieve by default server metrics except Tomcat ones.：就是ｊｍｅｔｅｒ只能监控ｔｏｍｃａｔ的主机）（前提是要先在服务器上ｓｔａｒｔＡｇｅｎｔ,即把插件解压到服务器上，运行ｓｔａｒｔＡｇｅｎｔ）

## 11、jp@gc - Reponse Codes per Second：

每秒返回的响应码，表明Jmeter测试期间，随着时间的推移返回的响应码，从中我们可看到测试期间在哪个时间段内出现了错误。就可以分析在该时间内系统的什么环境因素，导致的错误。

This graph will display the response code per second returned during the test.

## 12、jp@gc - Reponse Latencies Over Time：

每秒钟的响应等待时间， 表明Jmeter测试期间，随着时间的推移系统的响应等待时间的变化，也是系统随着时间推移，系统效率的变化。

This graph will display the response latencies during the load test. A latency is the duration between the end of the request and the beginning of the server response.（就是记录客户端发送请求完成后，服务器端返回请求之前这段时间）

## 13、jp@gc - Reponse Times Distribution：

响应时间分布， X轴表示的是响应时间，Y轴表示的是响应次数，F(X,Y)表示系统在某种响应时间内的响应次数是多少，如果在响应时间短的地方，响应次数多，说明系统的效率比较高。

This graph will display the response time distribution of the test. The X axis shows the response times grouped by interval, and the Y axis the number of samples which are contained in each interval.

## 14：jp@gc - Respose Times Over Time：

每秒钟响应时间，X轴表示的是系统运行的时刻，Y轴表示的是响应时间，F(X,Y)表示系统随着时间的推移，系统的响应时间的变化，可以看出响应时间稳定性。

This graph will display for each sampler the average response time in milliseconds.

## 15、jp@gc - Response Times Percentiles：

响应时间百分比，X轴表示的是百分比，Y轴表示的是响应时间，F(X,Y)表示低于某个百分比的响应时间，比如有80%的响应低于400ms。

This graph will display the percentiles for the response time values. X Axis represents percentage, Y Axis Response time values. One point (P, Value) means for the whole scenario, P percent of the values are bellow Value ms.（没太明白）

## 16：jp@gc - Response Times vs Threads：

响应时间用户数， X轴表示的是活动线程数，也就是并发访问的用户数，Y轴表示的是响应时间，F(X,Y)表示在某种并发量的情况下，系统的响应时间是多少。

This graph shows how Response Time changes with amount of parallel threads. Naturally, server takes longer to respond when a lot of users requests it simultaneously. This graph visualizes such dependencies.

## 17：jp@gc - Transaction Throughput vs Threads：

每活动线程数可能的事务吞吐量，途中X轴表示的是活动线程数，Y轴表示的是事务吞吐量，F(X,Y)的含义是当系统处于某个活动线程数时，系统当时的事务吞吐量是多少。比如当有10个活动线程时，事务吞吐量是100/s，而当有20个活动线程时，事务吞吐量是50/s，说明随着用户访问的增加，系统的处理效率开始下降了。从这个图中我们可以找到一个临界点，在多大的活动线程数时，系统达到最大的吞吐量。

This listenter is very similar to Response Times vs Threads, exept it shows total server's transaction throughput for active test threads.
 
## 18、jp@gc - Transactions per Second：

每秒的事务数，X轴表示访问结束的时刻，Y轴表示访问量，F(X,Y)表示在某个结束时刻，一共有多少的访问量结束访问。

This graph shows the number of transactions per second for each sampler. It counts for each seconds the number of finished transactions.

## Bytes Throughput Over Time

每秒传输字节吞吐量，表明Jmeter在测试时，随着时间推移发送和接受的字节数 

## Transaction Throughput Over Time

每秒处理的事务吞吐量  统计随着时间推移每秒可能的事务吞吐量 这里的事务吞吐量计算公式是： 活动线程数`*1秒/一个线程的响应时间，比如当一个用户向服务器发出一个请求，在100ms后得到响应，那么事务数等于1*1000ms/100ms = 10 transcation/s`，得到每秒钟可以处理是个事务数的结果。

<http://blog.163.com/zhang_jing/blog/static/4684588620113821922909/>