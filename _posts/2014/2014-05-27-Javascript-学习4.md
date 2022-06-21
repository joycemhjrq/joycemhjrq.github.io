---
layout: post
title: javascript学习4
categories: javascript
tags: javascript
---

## 范 例 5

在这一部分首先要为你展示的JavaScript特性是将你的滑鼠移到这个不同颜色的连结上面，此时看看浏览器下的状态列有何结果。然後这样的功能我们可以与JavaScript的功能相结合。好，现在再将你的滑鼠移到本处不同颜色的连结上面，你应该会发现有一个视窗出现，是吧？！如何！怎麽做到的呢？以下就是这一个连结的作法：

    <a href="tpage.htm" onMouseOver="window.status='Just another stupid link...'; return true" />

在这儿你只要在传统`<a>`的标签中加入onMouseOver的method，就可达成你要的效果了。这里的window.status是用来让你可以在WWW浏览器的状态列上显示一些讯息用的。在语法中，你可以看到讯息部分是用'括起来的部分，而非以"括起来，在讯息部分结束之後，必须加上;returntrue。好了，利用以上的特性可以很简单的完成第二个连结的例子！相当简单，以onMouseOver的method然後配合事件发生时去呼叫函数hello()就行了，不再多加解释了，作法如下：

{% highlight html %}

<html>
<head>
<script language="LiveScript">
<!-- Hiding
     function hello() {
       alert("哈 罗!");
     }
</script>
</head>
<body>
<a href="" onMouseOver="hello()">link</a>
</body>
</html>

{% endhighlight %}

## 范 例 6

接下来我们要告诉你一个使用日期和时间的例子。在第一部分中，你已看过了lastModified的用法和作法。现在要告诉你的并非是印出网路上伺服器或文件修改日期，而是你个人客户端机器的日期和时间。以下就是执行结果：

    今 天 日 期 为： 4/19/110

做法如下：

{% highlight html %}

<script language="LiveScript">
<!-- Hiding
  today = new Date()
  document.write("现 在 时 间 是： ",today.getHours(),":",today.getMinutes())
  document.write("<br>今 天 日 期 为： ", today.getMonth()+1,"/",today.getDate(),"/",today.getYear());
// end hiding contents -->
</script>

{% endhighlight %}

在本例中，我们必需首先建立一个日期变数，这可以由today=newDate()来完成。如果没有特别指定时间与日期的话，浏览器将会采用本地客户端机器的时间，若将它放入变数today中。这儿要注意的是：我们并没有宣告today这个变数的型态，这和Java或其他的程式语言在用到变数之前必需先加以宣告的方式有相当大的不同。在完成today的日期变数後，我们等於建立了一个具有本地时间与日期的物件(object)。接着就可以使用get...的method以取得today这个物件的时间和日期。请注意getMonth这个method所取得的月份范围是由0~11，所以必须加1以代表真正的1月至12月。看完以上的例子後，想想你可以使你的文件变得有点智慧，例如：某个文件有时效限制的话，你可以利用我们在上一部份的范例4中所提到的功能和这一个范例中所学习到的功能，设计一个让你的文件在第10天以後读到的话就会告诉读者："喂！这篇已过时了！"的程式，试试看，并不难！

除以上功能外，在建立日期物件时你也可以事先设定日期如下：

    docStarted= new Date(96,0,13)

首先是年（西元），接着是月（但记得减1），再接着是日。同样的方法也可以加上时间的设定，如下：

    docStarted = new Date(96,0,13,10,50,0)

前三个是日期的年、月、日，接着是时、分、秒。最後，我们必须提醒你，JavaScript并没有实际的日期型态，但是它却能毫不费力地显示出日期和时间，原因是它是从1/1/19700：0h开始以ms(milliseconds)来计算目前的时间的，这听起来似　有些复杂，但你倒不用担心，它有标准的共用函数可以计算，你只要知道如何用就可以了！
