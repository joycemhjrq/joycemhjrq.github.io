---
layout: post
title: loadrunner 脚本调试详细介绍
categories: loadrunner
tags: debug
---

lr的脚本大都已录制为主，而且经常有提出录制的脚本回放有问题，其实很多问题很好解决，LR提供了很多手段调试和优化我们的脚本。

## 1. 设置断点

相信大家都不陌生，LR也是可以设置断点的，在需要设置断点的语句前按F9快捷键，断点就设置好了，程序运行到断点语句后会暂停,这时我们可以用F10单步调试程序.

## 2. 打开EXtended Log

只是设置断点是不够的,我们还得知道具体发生了什么事情,Log告诉了我们一切,默认的Log是standard Log,这时远远不够的.我们要extended log,打开路径为runtime settings-->log-->extended log.把parameter substitution和data returned by server和advanced trace大家根据需要勾选吧.

## 3. 注释掉多余的语句

很多回放时出错的脚本都是因为多余的语句.因为LR在录制的时候,LR生成的语句很多不是我们想要的,这里结合几个网友的问题说说,曾有网友问:回放脚本的时
候 socket出错,提示: Error : socket0 - Address already in use. Error code: 10048.而且非常坚持说脚本不会有问题,因为脚本直接录制后回放,什么都没改,是不会出错的.拿到脚本一看,有一条语句 lrs_create_socket,建立了socket0,但是之后没有任何socket0的语句，可以判断出该语句是无效的，注释掉，问题解决。所以大家不要过分相信LR了，它生成的语句也是有很多BUG的。

经常有朋友们问到，log文件在哪看，特别是controll执行后，怎么看log。这里一一说明一下：

	（1）在vgen中，我们必须写输出函数输出信息，将我们所想要了解的信息用函数输出，主要有这么几个函数输出信息：lr_output_message,lr_error_message,lr_log_message。这些函数请参阅 help-->function reference.

	其次，我们要在runtime settings中设置，勾选always send messages,具体的做法是：runtime settings--->log-->always send messages，这样我们才能写出Log，在我们的脚本所在的文件夹中，有两个文件很重要，mdrv.log.txt和output.txt文件，lr_log_message只会把信息输到mdrv.log文件中，而lr_output_message则会写进以上两个文件。

	（2）在controller中，很多朋友都会想知道多次迭代，参数是否正确的导入了呢，我们依旧查看log,我们在执行结束后，查看结果目录的Log 文件夹,如果是负载生成器运行的话,则在tmp目录。不过，还想提醒朋友们，在controller我们也要设置runtime settings才行，而且每个用户组的runtime settings,设置的方法是：在controller的design标签页中，右下角的部分有runtime settings按钮，我们点击它，设置的方法与在vgen中一样的。
