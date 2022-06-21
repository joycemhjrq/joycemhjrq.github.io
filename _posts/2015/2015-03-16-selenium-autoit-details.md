---
layout: post
title: Selenium借助AutoIt识别(上传/下载)详解
categories: autoIt
tags: 
- selenium
- autoIt
---

AutoIt目前最新是v3版本，这是一个使用类似BASIC脚本语言的免费软件,它设计用于Windows GUI(图形用户界面)中进行自动化操作。它利用模拟键盘按键，鼠标移动和窗口/控件的组合来实现自动化任务。

官方网站：<https://www.autoitscript.com/site/>
从网站上下载AutoIt并安装，安装完成在菜单中会看到图4.13的目录：

<img src="/media/img/autoit/autoit-1.png">

图 1  AutoIt菜单

AutoIt Windows Info   用于帮助我们识Windows控件信息。  
Compile Script to.exe 用于将AutoIt生成 exe 执行文件。  
Run Script            用于执行AutoIt脚本。  
SciTE Script Editor   用于编写AutoIt脚本。  

{% highlight html %}

<html>
<head>
<meta http-equiv="content-type" content="text/html;charset=utf-8" />
<title>upload_file</title>
<link href="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
  <div class="row-fluid">
    <div class="span6 well">
    <h3>upload_file</h3>
      <input type="file" name="file" />
    </div>
  </div>
</body>
<script src="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.js"></script>
</html>

{% endhighlight %}


将上面的html代码保存为upload.html文件，通过浏览器打开，效果如下：

<img src="/media/img/autoit/autoit-2.jpg">

图 2

下面以操作upload.html上传弹出的窗口为例讲解AutoIt实现上传过程。

1、首先打开AutoIt Windows Info 工具，鼠标点击Finder Tool，鼠标将变成一个小风扇形状的图标，按住鼠标左键拖动到需要识别的控件上。

<img src="/media/img/autoit/autoit-3.png">

图 3 AutoIt Windows Info识别“文件名”输入框控件

<img src="/media/img/autoit/autoit-4.png">

图 4 AutoIt Windows Info识别“打开”按钮控件

如图3、4，通过AutoIt Windows Info 获得以下信息。

窗口的title为“选择要加载的文件”，标题的Class为“#32770”。

文件名输入框的class 为“Edit”，Instance为“1” ，所以ClassnameNN为“Edit1”。

打开按钮的class 为“Button”，Instance为“1” ，所以ClassnameNN为“Button1”。


2、根据AutoIt Windows Info 所识别到的控件信息打开SciTE Script Editor编辑器，编写脚本。

    ;ControlFocus("title","text",controlID) Edit1=Edit instance 1
    ControlFocus("选择要加载的文件", "","Edit1")

    ;Wait 10 seconds for the Upload window to appear
    WinWait("[CLASS:#32770]","",10)

    ;Set the File name text on the Edit field
    ControlSetText("选择要加载的文件", "", "Edit1", "D:\\upload_file.txt")
    Sleep(2000)

    ;Click on the Open button
    ControlClick("选择要加载的文件", "","Button1");

ControlFocus()方法用于识别Window窗口。WinWait()设置10秒钟用于等待窗口的显示，其用法与WebDriver 所提供的implicitly_wait()类似。ControlSetText()用于向“文件名”输入框内输入本地文件的路径。这里的Sleep()方法与Python中time模块提供的Sleep()方法用法一样，不过它是以毫秒为单位，Sleep(2000)表示固定休眠2000毫秒。ControlClick()用于点击上传窗口中的“打开”按钮。

AutoIt的脚本已经写好了，可以通过菜单栏“Tools”-->“Go” （或按键盘F5）来运行一个脚本吧！注意在运行时上传窗口当前处于打开状态。


3、脚本运行正常，将其保存为upfile.au3，这里保存的脚本可以通过Run Script 工具将其打开运行，但我们的目的是希望这个脚本被Python程序调用，那么就需要将其生成exe程序。打开Compile Script to.exe工具，将其生成为exe可执行文件。如图5

<img src="/media/img/autoit/autoit-5.png">

图 5 Compile Script to.exe生成exe程序

点击“Browse”选择upfile.au3文件，点击“Convert”按钮将其生成为upfile.exe程序。

4、下面就是通过自动化测试脚本调用upfile.exe程序实现上传了。

{% highlight python %}
#coding=utf-8
from selenium import webdriver
import os

driver = webdriver.Firefox()

#打开上传功能页面
file_path =  'file:///' + os.path.abspath('upfile.html')
driver.get(file_path)

#点击打开上传窗口
driver.find_element_by_name("file").click()
#调用upfile.exe上传程序
os.system("D:\\upfile.exe")

driver.quit()

{% endhighlight %}

通过Python 的os模块的system()方法可以调用exe程序并执行。

了解了上传的实现过程，那么下载也是一样的。