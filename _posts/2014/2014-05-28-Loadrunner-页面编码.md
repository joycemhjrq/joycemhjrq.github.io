---
layout: post
title: loadrunner 页面编码
categories: loadrunner
tags: encode
---

如何知道一个页面的编码是什么：查看页面源代码，在页面的head里面有这样：

	<meta http-equiv="Content-Type" content="text/html;charset=gb2312">   //编码为：gb2312

	<meta http-equiv="Content-Type" content="text/html; charset=utf-8" /> //编码为：utf-8

在lr中，如果用手写脚本的话，一定要注意编码格式，如果编码格式不正确的话，断言就会出错，这里的断言可以用lr_convert_string_encoding来处理，如当页面编码格式为：utf-8，但是本地编码却不是utf-8的时候，如果直接用页面上的文字来断言的话：

拿我们平台（编码为utf-8）为例：

	web_find("web_find",
	        "What=您好，欢迎来到好望角！",
	        LAST);

这时回放的时候就会出错，会报找不到：您好，欢迎来到好望角！
这句话，这是因为本地的编码不是为utf-8，那么回放的时候系统默认的格式也就不是utf-8，那在我们看来就是所谓的乱码，那么lr就找不到：您好，欢迎来到好望角！这句话，因为这句话经过系统编码后就成为我们所谓的乱码了。

可以用下面的方法来解决：

	lr_convert_string_encoding("您好，欢迎来到好望角！",  LR_ENC_SYSTEM_LOCALE, "utf-8", "aa"); 
	    web_find("web_find",
	        "What={aa}",
	        LAST);

这样把这句话修改为跟回放时一样的编码，那lr就可以在页面源代码中找到这句话了

手写脚本的话，无法设置页面编码，这时可以在你的脚本文件夹里面的*.cfg文件的［web］下面加入一行：
UTF8InputOutput=1 这样页面默认的输入输出的编码格式就是utf-8了。

解决了以上的问题的话，那手写代码又增进了一步。
