---
layout: post
title: javascript学习2
categories: javascript
tags: javascript
---

## 范例 2

接下来下一个例子所要介绍的是有关函数 (function) 的使用。请放心，函数并非很难懂的东西，但它却相当有用。函数通常是在 HTML 文件中 <body ＞的部份被呼叫，而理所当然地，它最好事先被宣告并放在 HTML 文件中 <body＞的部份。好让在 <body＞ 部分中使用到函数时，它已确定被读取住来。另外，<script＞ 标签的有关描述语法剖份，你可以用注解的符号将它括起来，以免旧版或无法读取 JavaScript 的浏览器读到，而误会了意思！

{% highlight html %}

<html>
 <head>
    <script language="LiveScript">    
        function pushresult(){
            document.write("好好学习，天天向上");
            alert("哈哈哈");
        }
    </script>
 </head>
 <body>
 <form>  
    <input type="button" name="button" value="Push me" onclick="pushresult" />
 </form>
 </body>
</html>

{% endhighlight %}

JavaScript 语法部份的结果如下，你可以试着按按钮看看有何结果产生！

<form>
<input type="button" value="Push me" name="Button1">
</form>

在范例 2 中，将会产生一个按钮，当你用滑鼠去按它的时候，会出现“好好学习，天天向上”的内容，以及一个弹出框：哈哈哈

这个结果是如何产生的呢？首先，在 <head＞内的函数会被载入并存於内存中，接着一个新的 <form＞标签 <input type ="button".....＞将产生一个接钮。然後，你可以在後面看到 'onClick' 的指令，这就是告诉浏览器，当该按钮被按时，应会执行onClick 後的函数 'pushbutton()'，而这个函数在刚刚程式被载入时就已安放在记忆体中了！请注意，在这个函数中我们用到了个新东西－ alert 的 method，是 JavaScript 事先定义好的，它会以对话视窗产生内涵的讯息，并有一"确定"(OK)的按钮。

## 范例 3： 

{% highlight html %}

<html>
 <head>
 <script language="LiveScript">
 <!--  hide script from old browsers
  function getname(str) {
    alert("哈罗! "+ str+"!");
  }
 // end hiding contents -->
</script>
</head>
 <body>
Please enter your name:
<form>
  <input type="text" name="name" onBlur="getname(this.value)" value="">
</form>
</body>
</html>

{% endhighlight %}

现在你可以试试结果如何： 

请输入你的名字： 
<form>
<i>
<input type="text" value="" name="name" gtbfieldid="2">
</i>
</form>

在这个例子中又有新的东西了。首先，让我们注意一下，在语法中的注解部分`(<!- ... -＞) `此部分即我们之前所提到的它可以避免旧版本或是不支援 JavaScript的 WWW 浏览器因为不认识这些函数而产生错误。它的顺序应 该为 <script＞先，接着为注解的开头 <!-，然後是内容，注解尾 -＞， 最後是 </script＞。另外要注意的一点是，语解尾那一行的开头双斜线 "//" ，不可以省略，它代表了JavaScript 的注解，若省略了的话， -＞之前的字会被误认为是 JavaScript 的指令。


 这个例子可以让使用者输入一段文字，然后点鼠标表示输入完毕後经由 <input＞标签中的"onBlur" 事件函数侦知，於是呼叫 Getname(Str)这个函数来加以取得输入字串，并将它以弹出框的形式显示在对话视窗上！函数 Getname(this.value) 中的 "this.value" 是你在文字输入格式中所输入的值。
