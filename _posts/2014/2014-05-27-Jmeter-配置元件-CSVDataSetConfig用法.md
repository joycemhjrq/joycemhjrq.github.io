---
layout: post
title: jmeter 配置元件 CSV Data Set Config用法
categories: jmeter
tags: 
    - jmeter
    - csv
---

## 一、

CSV Data Set Config的用法，如下图：

<img src="/media/img/jmeter-csv.jpg">

Filename：文件的具体地址以及名称如：E:\forTest\data\a.txt ；
File encoding :给出页面的编码方式，这里以百度为例，它的源代码里

    <meta http-equiv="content-type" content="text/html;charset=gb2312"> 

所以这里File encoding:gb2312

`Variable Names(comma-delimited)`:给出变量名如：a,b

这里的变量名是给后面引用用的，如要用到这个文件的值，可以利用变量名来引用：${a},${b},如a.txt文件中有这样的数据:jmeter,ant,那${a}就可以引用到jmeter,${b}就可以引用到ant

`Delimiter(use '\t' for Tab)`:这个是用来隔开变量的分隔符，如上面的a,b,那分隔符就是“,”

综上：CSV Data Set Config实现的功能跟之前用的：`${__CSVRead(${test.data.folder}/上架出售.txt,0)}`这个函数实现的功能大体上是一样的。不过`${__CSVRead(${test.data.folder}/上架出售.txt,0)}`这个函数还要在jmeter bin下的user.properties文件里面配置test.data.folder，然后在jmter 里面配置全局变量：test.data.folder的值为：`${__property(test.data.folder)}`

## 二、

利用Jmeter的http请求的时候，如果我们要添加不同的数据，而每一条数据都不一致，我们将每一天数据作为一个测试用例，则我们可能需要为每个测试用例建立一个http请求，十分麻烦，比如，就登录来说，有用户，密码，每个用户的用户名，密码都不一致，如果我们要建立多个用户登录的请求，每个用户使用不同的用户名和密码，我们可能要将每个用户名单独作为一个http请求，这样十分不方便。

我们是否可以将所有的用户名和密码写入一个文件，然后将http请求中的用户名和密码参数化，读取写有用户名和密码的文件呢？

利用Jmeter的CSV Data Set Config，可以实现这个功能，具体如下：

    1. 新建一个文本文件，里面保存要登录的用户名，密码，文件内容如下：
    admin,123
    manager,456
    test,test
    说明：这里用英文逗号为分隔符，也可以用其他为分隔符，在CSV Data Set Config中可以设置。
    2. 右键点击Jmeter中需要参数化的某个请求，选择添加——配置原件——CSV Data Set Config，会添加一个CSV Data Set Config，需要设置相关的一些内容，具体如下：
    Filename：文件名，，指保存信息的文件目录，可以相对或者绝对路径（比如：D:\ceshi.Txt）

    Variable Names：参数名称(如：有几个参数，在这里面就写几个参数名称，每个名称中间用分隔符分割，分隔符在下面的“Delimitet”中定义，为了和文件中的“,”对于，这里也用“,”分割每个参数名，（比如：use,password）
    Delimitet：定义分隔符，这里定义某个分隔符，则在“Variable Names”用这里定义的分隔符分割参数。
    Recycle on EOF：是否循环读入，因为CSV Data Set Config一次读入一行，分割后存入若干变量中交给一个线程，如果线程数超过文本的记录行数，那么可以选择从头再次读入
    设置CSV Data Set Config如下图所示：

    3. 在需要使用变量的地方，比如在登录操作中，需要提交的表单字段包含用户名密码，我们就可以用${变量名} 的形式进行替换，例如${user}和${password}
    4.附带说一下，通常有用户认证的地方都要用到cookie或者session，那么最好在测试计划中加入一个http cookie 管理器，直接添加一般不需要任何设置，否则可能会出现登录失败（在测试结果中，如果看到多次请求不同页面返回的字节数却是相同的，那多半是登录失败了）
    5.最后，添加后，可以通过 “添加－监视器－查看结果树(请求部分)”, 来检验参数化是否成功，运行线程组，如果失败，那么检查一下文本的路径，变量大小写等等，手册上说使用相对文本路径时，要以测试配置文件（默认是jmeter的bin目录）的目录为参考，但是我试过似乎不行，换成绝对路径就可以了


<img src="/media/img/jmeter-csv-2.jpg">