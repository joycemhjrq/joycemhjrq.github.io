---
layout: post
title: javascript学习6
categories: javascript
tags: javascript
---

若您同时想用frames和JavaScript的功能，首先您必需拥有NetscapeNavigator2.0(或以上)浏览器(至少目前是如此)。当然也有某些浏览器支援frames的功能--如:Oracle开发的PowerBrowser，但此浏览器还只是Beta版，目前尚不支援JavaScript。

首先，我稍微解释一下frames的功用。因为frames功能才开发不久，仍有许多HTML文件中未使用这项新功能。Frames最主要功用是"分割"您的视窗，使每个"小视窗"(frame)能显示不同的HTML文件(译按:这有点类似电视的子母画面)。更妙的是，不同frame之间可以互动(interact)，也就是说不同frame之间可以交换讯息与资料(information)。例如:假设您开了两个frames，第一个frame可显示普通HTML文件，第二个frame可显示工具列(toolbar)。此工具列中可包含浏览您homepage所需的各种按钮。如此一来，即使第一个frame载入了另一个HTML文件，您仍可在第二个frame中看到工具列。

现在我先将上述特性展示给您瞧瞧。请按一下下面的按钮，看一看frames的长相。(如果您是线上观看此文件，您可能需稍候一下才看得到结果，因为scripts必需由server中载入到您的机器)。

<form>
<font size="+1">
<input type="button" value="请 按 这 　" name="Button1">
</font>
</form>

以下是此frame的写法:

{% highlight html %} 
<HTML>
<HEAD>
<title>Frames</title>
</HEAD>
    <FRAMESET ROWS="50%,50%"> 
    <FRAME SRC="frtest1.html" name="fr1"> 
    <FRAME SRC="frtest2.html" name="fr2"> 
  </FRAMESET> 
</HTML>  

{% endhighlight %}

首先您必须告诉浏览器您要开几个frame?这是由<frameset...>这个标签(tag)来宣告。rows这项叁数是告诉浏览器您想将视窗分割成几列?而cols这项叁数是告诉浏览器您想将视窗分割成几行?您也可以用很多组的<frameset...>tags将视窗分割得更复杂。以下是Netsacpe所提到的一个范例:
 
    <FRAMESET COLS="50%,50%"> 
      <FRAMESET ROWS="50%,50%"> 
        <FRAME SRC="cell.html"> 
        <FRAME SRC="cell.html"> 
      </FRAMESET> 
      <FRAMESET ROWS="33%,33%,33%"> 
        <FRAME SRC="cell.html"> 
        <FRAME SRC="cell.html"> 
        <FRAME SRC="cell.html"> 
      </FRAMESET> 
    </FRAMESET> 

<form>
<font size="+1">
<input type="button" value="试 试 这 个 按 钮" name="Button2">
</font>
</form>

上面这个例子产生了两行，而且第二行又分割成三列大小相等的空间。在第一个<frameset>tag中的50%，50%两项叁数是用来表是frame的大小。

您可以给每个frame一个"名字"(name)。frame的名字在JavaScript语法中的地位非常重要。在本章的第一个范例中您已学到如何替frame命名。接下来您可以用<frame>tag告诉浏览器您要载入哪一个HTML文件。

我想您已了解frames的基本用法，接下来我们再看一个有趣的范例:

<form>
<font size="+1">
<input type="button" value="快 试 试 这 里" name="Button3">
</font>
</form>

上面的按钮将显示:按某个frame中的按钮後，会在另一frame中写入文字。

以下是此功能的原始码:

在产生frames效果之前，您需先制作下列原始码(frames.html):
 
{% highlight html %}

<HTML>
<HEAD>
<title>Frames</title>
</HEAD>
    <FRAMESET ROWS="50%,50%"> 
    <FRAME SRC="frame1.html" name="fr1" noresize> 
    <FRAME SRC="frame2.html" name="fr2"> 
  </FRAMESET> 
</HTML>

{% endhighlight %}

以下是frame1.html的原始码:
 
{% highlight html %}

<HTML>
<HEAD>
<script language="JavaScript">
<!-- Hiding
     function hi() {
       document.write("嗨!<br>");
     }
     function yo() {
       document.write("　!<br>");
     }
     function bla() {
       document.write("啦 啦 啦<br>");
     }
// -->
</script>
</HEAD>
<BODY>
这 是 第 一 个 frame!
</BODY>
</HTML>

{% endhighlight %}

以下是frame2.html的原始码:

{% highlight html %}

<HTML>
<body>
这 是 第 二 个 frame!
<p>
<FORM NAME="buttonbar">
     <INPUT TYPE="button" VALUE="嗨" onClick="parent.fr1.hi()">
     <INPUT TYPE="button" VALUE="　" onClick="parent.fr1.yo()">
     <INPUT TYPE="button" VALUE="啦" onCLick="parent.fr1.bla()">
</FORM>
</BODY>
</HTML>

{% endhighlight %}

哇!这些语法越来越长了!上述的语法是如何运作的呢?当使用者载入第一个档案(frames.html)後，将会产生两个frame，并且在第一个frame(命名为'fr1')中载入frame1.html，而在第二个frame(命名为'fr2')中载入frame2.html。到目前为止都只是普通的HTML语法。或许您已发现在frame1.html中包含了JavaScript语法，但此JavaScript并没有马上被执行。难到这些函式(functions)是不必要的?亦或是我　得删除这些不必要的函式?虽然我是　散惯的人，但这些函式确实是必须的。它们是被位於frame2.html中之JavaScript语法所呼叫而执行的。我在frame2.html中利用JavaScript语法制作了三个按钮，制作按钮来呼叫函式的方法我已在第一章提过，相信您对onClick的用法已相当熟悉。但是......parent.fr1是干嘛用的?

若您对物件观念已相当了解，相信它对您不是甚麽新鲜事。您可以看到frames.html同时呼叫frame1.html与frame2.html两个档案，所以frames.html称为frame1.html与frame2.html的parent(请恕我直接用英文)。同理，这两个新的frame就称为frames.html的child-frames。您可将这种复杂的关系想像成阶层式架构(hierarchy)。以下我借助一个小'图'来厘清这种关系:

 
                frames.html                parent
                 /      \
                /        \
               /          \
    fr1(frame1.html)     fr2(frame2.html)   children



当 然 您 可 依 此 类 推， 产 生 一 些 'grandchildren' frames。 (当 然， 这 并 不 是 正 式 的 名 称):

 
                frames.html                parent
                 /      \
                /        \
               /          \
    fr1(frame1.html)     fr2(frame2.html)   children
            /  \
           /    \
          /      \
      gchild1  gchild2                    'grandchildren'      



若您想由frame2.html呼叫parent-frame中的任何函数，您只要将parent放在您欲呼叫函数名称之前即可。同理，若由parent-frame呼叫位於frame1.html中的函数，您只需将fr1放在被呼叫函数名称之前。为什麽用fr1呢?原因是我们在frames.html中开了两个frame，而且将它们分别命名为fr1和fr2。所以我用fr1代表第一个frame(frame1.html)。接下来的步骤就相当容易了。当我们欲由frame2.html(命名为fr2)呼叫第一个frame中的函式时该怎麽做?由上面的"小图"您可以发现，frame1.html和frame2.html之间并无直接关连(connection)，因此您不可以"直接"由frame2.html呼叫位於frame1.html中的函式。您必须透过parent-frame来呼叫它，所以正确指标(index)应为parent.fr1。例如若您想由frame2.html呼叫hi()，您必须写成parent.fr1.hi()。这也就是为什麽frame2.html中的onClick要写成那种样子的原因了。

对了!附带提一点小东西。您或许已发现本章中都用<scriptlanguage="JavaScript">代替前两章用的<scriptlanguage="LiveScript">。其实此二者间并没什麽差别。但起初JavaScript被植入Netscape浏览器时，您只能用LiveScript语法。(这是一种由Netscape发展的旧语法，和JavaScript极为类似)。我想既然我们讨论的是JavaScript函式，就应使用JavaScript语法。(这只是我的一己之见，在'JavaScript-society'中有许多关於LiveScript的讨论，目前也是众说纷纭....)

在此我要花一点时间讨论一个很多人关心的问题。当您观赏一个拥有数个frame的网页时，若您想link至internet其它网页，这些frame并不会消失而且会带来视觉上的干扰。这些讨厌的frame应该如何消除呢?

您只要在您的<ahref...>tag中加入TARGET="_top"就可解决这个困扰。方法如下:

<a href="goaway.html" TARGET="_top">如果您不想观赏我的网页了</a>

当然，您必须在您想要获得此效果的link中都加入TARGET="_top"。若想要整个网页中的link都有此效果，您可在此网页的head中加入<basetarget="_top">较方便。如此一来每个link被按下後都可将frame消除。