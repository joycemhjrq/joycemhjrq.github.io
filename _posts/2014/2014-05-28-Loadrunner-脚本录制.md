---
layout: post
title: loadrunner 脚本录制
categories: loadrunner
tags: record
---

## 录制脚本

loadRunner录制脚本时，通常情况下，将登录到服务器的活动记录在`vuer_init`部分中，将客户端活动录制到Actions部分中，并将注销过程录制到`vuser_end`部分中。因为运行多次迭代的Vuser脚本时，只有脚本的Actions部分重复，而`vuser_init`和`vuser_end`部分将不重复。

录制完脚本后，回放脚本过程中有时会发生中断或停止的情况，查看错误时，如果无法找到SOAP文件字样时，就需要考虑更换脚本录制协议了。通常首先考虑更换Web Services协议，再次录制脚本，问题就相应解决了。

## HTML_based录制 和 URL_based录制

Recording:选择录制方式：

`html_based script`和`URL_based script`，选择哪种录制方式，有以下参考原则：

	1、基于浏览器的应用程序推荐使用html_based script;
	2、不是基于浏览器的应用程序推荐使用URL_based script;
	3、如果基于浏览器的应用程序中包含了javascript，关且该脚本向服务器发送了请求，比如DataGrid的分布按钮等，推荐使用URL_based script;
	4、基于浏览器的应用程序中使用了HTTPS安全协议，建议使用URL_based script方式录制；
	5、如果使用HTML_based script模式录制后不能成功回放，可以考虑用URL_based script模式来进行录制，因为这种情况多是上面所列举的情况所引起的。

`（Tools-->Recording Options-->Recording-->(HTML_based script  or  URL_based script)）`

## 脚本录制窍门

如何在一个脚本中实现不同事务不同次数的循环呢？

案例：假如你想在一个脚本中，实现登录执行1次，查询执行2次，插入执行3次，怎么办？录3个脚本？每个事务分别在脚本中复制N次？

当然不用，LR早就想到了你的需求，下面让我们隆重推出Block。

位置：

Run-time Settings--General--Run Logic

操作：

	1.将你所要考察的事务设置在不同的Action内。
	2.在Run Logic中的Run中删掉默认的Action。
	3.在Run中插入Block。
	4.在插入的Block中再插入我们要考察的Action。
	5.设置Block的properties。这里有两种选择，Sequential和Random。如果选择Sequential，在下面的 Iteration中直接填入数值，那么Block中的Action都会按输入的次数执行。如果选择Random，下面的properties还可以设置 Block内各Action执行的百分比。

按照我们前面的案例，我们只需要设置3个Block，每个Block中分别插入一个Action，设置执行次数分别为1，2，3就可以了。
 
本人理解补充

	1、如果脚本中各个action没有顺序或逻辑关系，Block中action顺序可以是任意的。如查询。但是像登录这样必须在前面执行的action，随意放置将导致脚本失败。
	2、在Number of Iterations中设置的循环次数，作用于Run(x)下的所有Action，而不作用于Block下的action。即Block下的action可以通过设置Block的Properties来指定循环的次数。

