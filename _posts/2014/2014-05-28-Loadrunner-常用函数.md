---
layout: post
title: loadrunner 常用函数
categories: loadrunner
tags: method
---

## Web_submit_form和Web_submit_data

在LoadRunner中有两个常用函数:`Web_submit_form`和`Web_submit_data`，在群里有人问这两个函数有什么区别。为什么会有两个不同却功能相似的函数。区别在哪里。

首先，从工具的角度来说，厂商推荐使用`Web_submit_form`函数，因为这个函数看起来更易用，需要关注的东西较少。但是，从个人的角度来说，我推荐使用`Web_submit_data`函数。因为这个函数提供更多技术细节。在测试的过程中可控性更高。下面我就详细解释一下两个函数的工作机制：

首先看一下下面这段HTML代码

	<Form Action=login.asp Method=“POST”>
	<input name=user value=“”>
	<input name=password value=“”>
	<input type=hidden name=sessionID value=15379>
	</Form>

在录制的过程中，当打开这个页面的时候，这个页面会放在LR的Cache中，之后当我们输入了用户名、口令之后，点了一下提交。Browser会向LR Record Proxy发送一个提交请求，提交内容应该是这样的：

	POST login.asp HTTP/1.1
	user=steve password=buba
	sessionID=15379

这时候，LR会自动比较提交的内容和Cache的内容，首先它会比较提交的数据项和Cache中的数据项是否一致。页面中有三个输入域user、 password、sessionID，而提交的内容也有这三项数据，所以它认为提交数据使用了Cache中的页面，之后它会继续比较具体数据的值。它会发现sessionID的值和Cache中的值是一样的。但是user和password的值不一样。

这时候，`Web_submit_form`和`Web_submit_data`的区别就出现了：

	web_submit_form(”start",

	ITEMDATA,

	"name=user", "value=steve", ENDITEM,

	"name=password", "value=bean", ENDITEM,

	LAST);

	web_submit_data(”start",

	“Action=login.asp”,

	“Method=POST”

	ITEMDATA,

	"name=user", "value=steve", ENDITEM,

	"name=password", "value=bean", ENDITEM,

	"name=sessionID", "value=15379", ENDITEM,

	LAST);

可以看到，`Web_submit_form`只提供了和Cache中有差别的数据，其余的数据会自动从Cache中取。而`Web_submit_data`则提供了所有的数据，不管Cache存在不存在`Web_submit_data`都是可以工作的。

所以厂商会推荐使用`Web_submit_form`，因为它看起来更易用，甚至关联都不需要作，就能直接回放。但是这种情况只能对于简单系统适用。对于一些银行或者移动的复杂系统来说，有时侯会对Cache作特殊操作，`Web_submit_form`有时侯就会报一些莫名其妙的错出来。而 `Web_submit_data`则跟Cache内容无关。稳定性和可控性都要比`Web_submit_form`要高很多。所以个人推荐尽量使用 `Web_submit_data`函数。

## lr_save_string

	Action()
	{
	 char *a="hello world中国";
	  char *b="hello baidu";
	  lr_log_message("%s,%s",lr_eval_string(a),lr_eval_string(b));

	  lr_save_string("John Doe" , "Student" );
	  lr_log_message("JJJ:%s",lr_eval_string("{Student}"));
	    return 0;
	}
	Replay log:
	Running Vuser...
	Starting iteration 1.
	Starting action Action.
	hello world中国,hello baidu
	JJJ:John Doe
	Ending action Action.
	Ending iteration 1.
	Ending Vuser...

## web_custom_request：

允许使用HTTP支持的任何方法来创建自定义HTTP请求

## web_image：

在定义的图像上模拟鼠标单击

## web_link：

在定义的文本链接上模拟鼠标单击

## web_submit_data：

执行“无条件”或“无上下文”的表单

## web_submit_form：

模拟表单的提交

## web_url：

加载由“URL”属性指定的URL

## web_add_cookie：

添加新的Cookie或修改现有的Cookie

## web_set_timeout：

指定Vuser等待执行指定任务的最长时间

