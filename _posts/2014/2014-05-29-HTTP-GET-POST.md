---
layout: post
title: HTTP GET与POST方法的区别
categories: HTTP
tags: 
    - GET
    - POST
---

1. get是从服务器上获取数据，post是向服务器传送数据。
2. `get是把参数数据队列加到提交表单的ACTION属性所指的URL中，值和表单内各个字段一一对应，在URL中可以看到。`post是通过HTTP post机制，将表单内各个字段与其内容放置在HTML HEADER内一起传送到ACTION属性所指的URL地址。用户看不到这个过程。
3. 对于get方式，服务器端用Request.QueryString获取变量的值，对于post方式，服务器端用Request.Form获取提交的数据。
4. get传送的数据量较小，不能大于2KB。post传送的数据量较大，一般被默认为不受限制。但理论上，IIS4中最大量为80KB，IIS5中为100KB。
5. get安全性非常低，post安全性较高。
 
HTTP请求：GET与POST方法的区别

HTTP 定义了与服务器交互的不同方法，最基本的方法是 GET 和 POST。事实上 GET 适用于多数请求，而保留 POST 仅用于更新站点。根据 HTTP 规范，GET 用于信息获取，而且应该是安全的和幂等的。所谓安全的意味着该操作用于获取信息而非修改信息。换句话说，GET 请求一般不应产生副作用。幂等的意味着对同一 URL 的多个请求应该返回同样的结果。完整的定义并不像看起来那样严格。从根本上讲，其目标是当用户打开一个链接时，她可以确信从自身的角度来看没有改变资源。比如，新闻站点的头版不断更新。虽然第二次请求会返回不同的一批新闻，该操作仍然被认为是安全的和幂等的，因为它总是返回当前的新闻。反之亦然。POST 请求就不那么轻松了。POST 表示可能改变服务器上的资源的请求。仍然以新闻站点为例，读者对文章的注解应该通过 POST 请求实现，因为在注解提交之后站点已经不同了（比方说文章下面出现一条注解）；

在FORM提交的时候，如果不指定Method，则默认为GET请求，Form中提交的数据将会附加在url之后，以?分开与url分开。字母数字字符原样发送，但空格转换为“+“号，其它符号转换为%XX,其中XX为该符号以16进制表示的ASCII（或ISO Latin-1）值。GET请求请提交的数据放置在HTTP请求协议头中，而POST提交的数据则放在实体数据中；
GET方式提交的数据最多只能有1024字节，而POST则没有此限制。
 
在表单里使用”post”和”get”有什么区别
 
在Form里面，可以使用post也可以使用get。它们都是method的合法取值。但是，post和get方法在使用上至少有两点不同：

1、Get方法通过URL请求来传递用户的输入。Post方法通过另外的形式。

2、Get方式的提交你需要用Request.QueryString来取得变量的值，而Post方式提交时，你必须通过Request.Form来访问提交的内容。

仔细研究下面的代码。你可以运行之来感受一下：

代码
	<!--两个Form只有Method属性不同-->
	<FORM ACTION=“getpost.asp” METHOD=“get”>
	<INPUT TYPE=“text” NAME=“Text” VALUE=“Hello World”></INPUT>
	<INPUT TYPE=“submit” VALUE=“Method=Get”></INPUT>
	</FORM>
	<BR>
	<FORM ACTION=“getpost.asp” METHOD=“post”>
	<INPUT TYPE=“text” NAME=“Text” VALUE=“Hello World”></INPUT>
	<INPUT TYPE=“submit” VALUE=“Method=Post”></INPUT>
	</FORM>

	<BR>
	<BR>

	<% If Request.QueryString(“Text”) <> ““ Then %>
	通过get方法传递来的字符串是： “<B><%= Request.QueryString(“Text”) %></B>“<BR>
	<% End If %>

	<% If Request.Form(“Text”) <> ““ Then %>
	通过Post方法传递来的字符串是： “<B><%= Request.Form(“Text”) %></B>“<BR>
	<% End If %>

说明

把上面的代码保存为getpost.asp，然后运行，首先测试post方法，这时候，浏览器的url并没有什么变化，返回的结果是：

通过Post方法传递来的字符串是： "Hello World"

然后测试用get方法提交，请注意，浏览器的url变成了：

http://localhost/general/form/getpost.asp?Text=Hello+World

而返回的结果是：

通过get方法传递来的字符串是： "Hello World"

最后再通过post方法提交，浏览器的url还是：

http://localhost/general/form/getpost.asp?Text=Hello+World

而返回的结果变成：

通过get方法传递来的字符串是： "Hello World"

通过Post方法传递来的字符串是： "Hello World"


提示

通过get方法提交数据，可能会带来安全性的问题。比如一个登陆页面。当通过get方法提交数据时，用户名和密码将出现在URL上。如果：

１、 登陆页面可以被浏览器缓存；

２、 其他人可以访问客户的这台机器。

那么，别人即可以从浏览器的历史记录中，读取到此客户的账号和密码。所以，在某些情况下，get方法会带来严重的安全性问题。
建议

在Form中，建议使用post方法。
