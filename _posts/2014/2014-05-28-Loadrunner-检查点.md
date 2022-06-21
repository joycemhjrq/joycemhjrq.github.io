---
layout: post
title: loadrunner 检查点
categories: loadrunner
tags: check
---

## 文本检查点

发现要用文本检查点的话   例：

	web_find("web_find",
	        "RightOf=关",
	        "LeftOf=好望角",
	        "What=于",
	        LAST);

（可以用RightOf，LeftOf，也可以不用，RightOf：定义查找字符（What的内容）的右边界，LeftOf：定义查找字符（What的内容）的左边界）

web_find()不能检查分层里面的东西（查看页面源代码的时候，那些内容是在div里面的，或者从页面上看直接它是分成左右两框（块）的等）。       -----真是麻烦呀！！！！

还有web_find()的RightOf和LeftOf刚好相反（这里要注意的一个是，不能单从页面上看要查找的东西的左右，因为从页面源代码里看的话，你或许会发现，它都不止页面显示的东西，可能还有链接）：就拿上面的例子来说，在源代码里和页面里显示的是“我的好望角”，定义要查找的字符为“于” 即：“What=于”，按RightOf和LeftOf的定义来看的话，它们应该是：

	web_find("web_find",
	        "RightOf=好望角",
	        "LeftOf=关",
	        "What=于",
	        LAST);

但实际上这样是错的，这样写才是对的：

	web_find("web_find",
	        "RightOf=关",
	        "LeftOf=好望角",
	        "What=于",
	        LAST);

也就是说它RightOf和LeftOf的定义刚好相反。。。。。

不知道loadrunner还有多少未知的bug，真是，原本以为它是多么好用的东西，它在心目中的形象一点点幻灭了！
又仔细地想了想，其实从另一个角度理解它可以是正确的，如”RightOf=关“ ，”LeftOf=好望角“ ，”What=于“ 的话，

可以理解为，”关“的右边是”于“；”好望角“的左边是”于“

唉，一个收费的软件还做得这么烂，连定义都这么模糊，晕了！！！！！！！！！

录制脚本的时候要把Tools-->Recording Options的Advanced的Support charset选为UTF-8，如果没有选的话，在回放的时候加入文本检查点很可能会过不了（注意这里是在录制的时候修改)。

## 图片检查点

用loadrunner录制脚本后，插入图片检查点如：

	web_image_check("web_image_check",
	  "Src=/style/red/img/logo.gif",
	  LAST);

很有可能会出现错误如下：

Action.c(13): Error -27985: There is no context for HTML-based functions. A previous function may not have used "Mode=HTML" or downloaded only non-HTML page(s), or the context has been reset (e.g., due to a GUI-based function)   [MsgId: MERR-27985]
Action.c(13): web_image_check highest severity level was "ERROR"   [MsgId: MMSG-26391]

此时如果你找了很久都没有找到原因的话，很有可能是这个问题：

你加入的图片check为：  Src=/style/red/img/logo.gif,

但实际你查看页面源代码时看到它的显示是这样的：    Src="/style/red/img/logo.gif",它比你加入的check多一对双引号，这时你又不能人为在check图片里加入双引号的话是不行的，因为

loadrunner是不认这种双引号的，在loadrunner代码里\"代表双引号，但是用这种形式的话

loadrunner还是读不了，我想这应该是loadrunner的一个漏洞吧，不知道还有没有别的办法解决这个问题？

上面那段是之前检查出问题后分析出的的loadrunner检查不到图片出的问题，之后又经过一段时间的摸索发现其实问题不是双引号引起的，而是对于图片属性的定义出错引起的，如如果录制一个页面，如果用这个页面的一个图片作为检查的话，点击这个图片，查看属性如果为：http://csdnimg.cn /www/images/csdnindex_piclogo.gif

这时对于检查点的写法，只能是Src为：/www/images/csdnindex_piclogo.gif这样的话是可以成功的但：

如果Src定义为：http://csdnimg.cn/www/images/csdnindex_piclogo.gif的话就会出现之前的 Error -27985错误，

还有ALT不用定义，定义的话也可能会出错。

根据进一步的学习，发现要用图片检查点的话，它的src定义可以直接点右键，查看页面源代码，看源代码里src是怎么写的，如果源代码里`src=“images/csdnindex_piclogo.gif”的话，你在loadrunner里写成“src=/images/csdnindex_piclogo.gif”`都是错的，只不过多一个斜杠都不行，看来这里src的写法要非常的谨慎，稍有一点问题就会导致脚本运行不过，看来loadrunner还真的是不好用啊，一点都不普及。 
