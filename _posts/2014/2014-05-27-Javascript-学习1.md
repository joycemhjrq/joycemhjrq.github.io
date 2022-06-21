---
layout: post
title: javascript学习1
categories: javascript
tags: javascript
---

[原文](http://www.lib.tsinghua.edu.cn/chinese/INTERNET/JavaScript/)

JavaScript 是一种新的描述语言，此一语言可以被箝入 HTML 的文件之中。 透过 JavaScript 可以做到回应使用者的需求事件 (如： form 的输入) 而不用任何的网路来回传输资料，所以当一位使用者输入一项资料时，它不用经过传给伺服端 (server)处理，再传回来的过程，而直接可以被客户端 (client) 的应用程式所处理。范例 1：
 
HTML 文件中：

{% highlight html %}

<html>
<head>
My first JavaScript!
</head>
<body>
<br>
This is a normal HTML document.
<br>
  <script language="LiveScript">
    document.write("这是以 JavaScript 印出的!")
 </script>
<br>
Back in HTML again.
 </body>
 </html>

{% endhighlight %}

以上范例的结果如下： 

    This is a normal HTML document.

    Back in HTML again.

此一范例并没有太大的用处，它只是要告诉你如何使用<script>的标签，并如何将它置於 HTML的文件之中而已，这个新的标签你可以特它放在文件中的任何地方。
