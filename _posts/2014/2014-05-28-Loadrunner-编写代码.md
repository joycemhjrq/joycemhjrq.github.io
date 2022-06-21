---
layout: post
title: loadrunner 编写代码
categories: loadrunner
tags: code
---

## 编写代码

lr编写代码的时候有两个注意事项：

lr比较重要的一个文件是它的：default.cfg文件，lr回放时一些主要的信息都存放在它的里面，比如页面编码，重定向等问题

	1、编码问题，手动编写的时候，即使你在Recording Option中设置了utf-8，它回放的时候也不会以utf-8的编码格式回放，还是会以本地编码格式回放，因为这个设置只针对录制（Recording）。解决办法：在default.cfg文件的［web］下面加入：UTF8InputOutput=1 ；
	2、重定向问题，手动编写的时候，如果遇到http向https的跳转，会跳转到一个重定向的页面，解决办法：在在default.cfg文件的［web］下面加入：CustomUserAgent=Post_Multipart。

啊哈哈哈，解决了昨天的utf-8问题跟今天的重定向问题，真爽！！

	1、在项目的.bak文件中定义了参数之类的一些东西，如果因为参数出错引起的问题，可以到这个文件里面查看下是什么问题
	2、脚本注释用//注释一行
	用注释多行 

## import class的路径设置

{% highlight java %}

	import lrapi.lr;
	import com.goodhope.HelloWorld;

	public class Actions
	{

	    public int init() throws Throwable {
	        return 0;
	    }//end of init


	    public int action() throws Throwable {
	               HelloWorld hw=new HelloWorld();
	           hw.main(null);
	        return 0;
	    }//end of action


	    public int end() throws Throwable {
	        return 0;
	    }//end of end
	}

{% endhighlight %}

在这里要使loadrunner能够读到：import com.goodhope.HelloWorld;我们可以在loadrunner的runtime settings里面添加classpath，然后把相应的这个包（com包）放到相应路径下面loadrunner就可以读到这个class文件了，如classpath其中一个路径为：D:\loadRunner\HelloWorld\bin，而HelloWorld.class在：D:\loadRunner\HelloWorld\bin\com\goodhope下面，那个就可以认到这个包了。（当然在这之前要设置好你系统的java路径，要不然系统都无法编译java就不用谈import了）

在loadrunner里面java代码不太好写，可以结合eclipse写好代码，然后把相应的包拷到相应的目录下，在loadrunner里面import进去就可以了

