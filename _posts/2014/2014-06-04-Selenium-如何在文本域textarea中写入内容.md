---
layout: post
title: Selenium 如何在文本域textarea中写入内容
categories: Selenium
tags: textarea
---

selenium之前的版本解决办法：

textarea本身是有默认值的，默认值下方是加载的一个FCK编辑器，在编辑器加载完后会清空赋给textarea的值。所以昨天给textarea赋值，就是看不到效果，以为是没有赋值上。

源码是这样的：

	<textarea id ="propJournal.content" name ="propJournal.content"  rows="10" cols="74">编辑框加载中,请稍等...</textarea>
	<script type="text/javascript" src="http://css.mysupa.com/fckeditor/fckeditor.js">

解决办法是，先加载FCK的js。等js加载好了，在给编辑框赋值，这样就不会给清空了。

selenium的代码我是这样写的：

	selenium.runScript("FCKeditorAPI.GetInstance('propJournal.content').SetHTML('hello, world')");

这样运行下来，编辑器就被赋值了。能正常运行。

selenium2解决办法：

源代码：

	<textarea name="licence.content" cols="100" rows="10" id="editor">
	</textarea>
	<script type="text/javascript" src="/ckeditor/ckeditor.js?t=B37D54V"></script>

	((JavascriptExecutor)driver).executeScript("CKEDITOR.instances['editor'].se 
	tData('stuff');");

这样就在框内输入了：stuff

