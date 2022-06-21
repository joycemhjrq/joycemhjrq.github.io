---
layout: post
title: javascript学习5
categories: javascript
tags: javascript
---

## 范 例 7

接下来我们要为你介绍一个可以产生乱数的函数，也是以JavaScript所写的。这个函数只是利用了一点小技巧而已，而这种技巧在大部分的编译器(compiler)中，大都是如此（或类似）计算出乱数来的。我相信JavaScript最後应也会以相似的方法来产生这样的method，如果它会提供这样功能的话。以下是此函数的结果：这是一个计算产生的乱数：

以下是这个作法的写法：

{% highlight html %}

<html>
<head>
<script language="LiveScript">
function RandomNumber() {
  today = new Date();
  num = Math.abs(Math.sin(today.getTime()));
  return num;  
}
</script>
</head>
<body>
<script language="LiveScript">
<!--
  document.write("This is a random number:", RandomNumber());
// -->
</script>
</body>
</html>

{% endhighlight %}

我们的做法是以上一个范例中的时间函数；它会出现一个很大的数，利用这个数再加以运算即可！例如：将它拿来做正弦函数（sin）的运算，得到的数再做绝对值的运算，结果可以得到一个介於0与1间的实数。因为时间的改变是ms为单位，而且你绝不会获得相同的数字。不过这个做法并不适合拿来快速的连续产生一系列的乱数，但如果你是不定时，久久的用一次，那效果就不错了！

## 范 例 8

JavaScript的一个重要特点是它可以制作视窗。你可以产生一个的视窗，并且在此视窗中载入HTML文件，完全以JavaScript来航游网际网路（Internet）。接下来的范例就是告诉你如何开启一个视窗并且写点东西进去，你可先试试按一下范中之接钮看看！

<form>
<input type="button" value="Push me" name="Button1">
</form>

原始程式如下：

{% highlight html %}

<html>
<head>
<script language="LiveScript">
function WinOpen() {
   msg=open("","DisplayWindow","toolbar=no,directories=no,menubar=no");
   msg.document.write("<HEAD><TITLE>哈 罗!</TITLE></HEAD>");
   msg.document.write("<CENTER><H1>酷 毙 了!</H1><h2>这 是<B>JavaScript</B>所 开 的 视 窗!</h2></CENTER>");
}
</script>
</head>
<body>
<form>
<input type="button" name="Button1" value="Push me" onclick="WinOpen()">
</form>
</body>
</html>

{% endhighlight %}

这儿你又看到用按钮来启动函数。这次的函数WinOpen()是藉着一个open的method来产生一个新的视窗。第一对双引号("")是用来描述欲载入至视窗中的文件URL位置。如果留者空白的话，那就会呈现一个空白视窗，并可以透过JavaScript写入东西！下一对双引号内的内容是这个新开启视窗的名字，你可以喜欢给它任意一个名字，但不可加入空白，如用DisplayWindow的话便会出现错误讯息，请注意。接下来的一连串双引号内所指定的是该视窗的相关性质(properties)，这些蛮有趣的，你可以指定要不要有工具棒(toolbar)、卷轴(scrollbar)，等等，例如：如果你写toolbar=yes，那就会在你所产生出来的视窗中出现一排工具列。以下有许多你可以改变的视窗特性，请注意它们字中间不可以有空白：

    toolbar
    location
    directories
    status
    menubar
    scrollbars
    resizable
    copyhistory
    width=pixels
    height=pixels

以上的pixels部分，你可以填入点数的数值，这数值是告诉浏览器这个视窗的大小。在开启视窗，并且将它称为msg以後，就可以开始写些东西到视窗中了。你可以写入一般正规的HTML语法文字。哇！这可不得了了，也就是说，你可以利用先前使用者在form表格中输入给你的文章直接将它回应至浏览器上来了！这些功能在几个月前大概还只能用CGI才能达到喔！现在你不用CGI也可以做了！