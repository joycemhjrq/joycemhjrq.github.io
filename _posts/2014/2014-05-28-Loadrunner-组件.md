---
layout: post
title: loadrunner 组件
categories: loadrunner
tags: component
---

## loadrunner组件

lr包含很多组件，其中最常见的有Vuser发生器、控制台、分析器和负载发生器。

	1、Vuser发生器（Visual User Generator，简称为VuGen）用来捕获最终用户业务流程和创建自动化性能测试脚本，即产生测试脚本。

	2、控制台（Controller）是用来运行的监视性能测试脚本的平台。即模拟真实环境，根据实际环境设置运行负载计划，进行实时控制。

	3、分析器（Analysis）是测试结果分析器，它是分析系统性能指标的一个主要工具。

	4、负载发生器（Load Generators）产生虚拟客户端的请求，用来在不同的测试服务器上创建负载，这些负载代理生成器开始和结束于控制台的程序。

在每次执行的末尾，控制台将自身的管理日志和来自负载发生器的日志合并，生成分析器程序可用的文件。然后分析器程序可以创建运行结果报告和图表给 Microsoft Word、Crystal Reports或者一个HTML网页流览器。每个由分析器产生的HTML报告页都包含到结果文本文件的链接。这些文本可用Microsoft Excel打开进行进一步的分析。每次运行的错误信息被存储在数据库中，可通过Microsoft Access进行读取。
