---
layout: post
title: javascript学习3
categories: javascript
tags: javascript
---

## 范例 4

这个范例更是帅了！我们在 HTML 文件档完成了以後，常会加上一行文件最後修改的日期，现在你可不用担心每次都要去改或是忘了改了。你可以很简单的写一个如下的描述语法程式，就可以自动的为你每次产生最後修改的日期了：

{% highlight html %}

<html>
 <body>
    This is a simple HTML- page.
 <br>
    Last changes:
 <script language="LiveScript">
 <!--  hide script from old browsers
    document.write(document.lastModified)
  // end hiding contents -->
 </script>
 </body>
</html>

{% endhighlight %}

以上的 document.lastModified 叁数在 Netscape 2.0 beta 2 版时是被写成 documeut.lastmodified 的，然而，之後的版本就改为 document.lastModified，所以注意一下 ；JavaScript 本身是会区分大小写的， lastmodified 与 lastModified在它看来是不同的结果。

最後，在这一部分结束之前，要提醒你一点，像范例 4 ，的用法并非每一部机器都是一样的，例如：PC 上跑得很正确的，在工作站上不一定会有相同的结果，所以，或许你仍得测一测不同机器的结果才会有所定论。当然，这一切是因为 JavaScript 还正在发展的原因，最新的讯息还是得去拜访一下Netscape公司才知道。也许你也不用奇怪，当你隔周再来访时，JavaScript 可能又作了相当大的改变了呢！

第一章：完
