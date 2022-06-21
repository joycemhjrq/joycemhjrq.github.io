---
layout: post
title: Selenium Grid简介与使用
categories: Selenium
tags: Grid
---

## 简介

Selenium Grid是一种自动化的测试辅助工具，Grid通过利用现有的计算机基础设施，能加快Web-app的功能测试。利用Grid，可以很方便地同时在多台机器上和异构环境中并行运行多个测试事例。

Selenium Grid基于Web-app测试工具Selenium，它可以让您同时并行运行多个Selenium Remote Control。比较好的一点事，它使所有这些Selenium Remote Control显示为一个，这样您在测试中就可以不必操作具体的计算机。

Selenium Grid因为是基于Selenium RC的，所以它同时支持RC的其它所有语言，如：Ruby, Java, Python, C#, PHP, ...

还有最后一点，Selenium Grid简单易用。

## 原理图

<img src="/media/img/selenium-grid.jpg">

## 使用

下面，我们就介绍下如何使用Selenium Grid。

准备：

1. 下载安装ant

http://apache.mirrormax.net/ant/binaries/apache-ant-1.7.0-bin.zip

解压包到你选择的任意目录，然后将 你的解压路径/apache-ant-1.7.0/bin 添加的Path变量中。

最后验证安装：

$ ant -version

Apache Ant version 1.7.0 compiled on December 13 2006

2. 下载安装JDK 1.6

http://www.java.com/en/download/index.jsp

安装后添加java的bin目录到path变量，然后验证：

	$java -version

	java version "1.6.0"

	Java(TM) SE Runtime Environment (build 1.6.0-b105)

	Java HotSpot(TM) Server VM (build 1.6.0-b105, mixed mode)

3. 下载Selenium Grid

http://selenium-grid.seleniumhq.org/download.html

其中，zip是Windows平台的，tar.bz2用于其他平台。下载后解压缩，然后验证：

$cd <你的grid目录>

$ant sanity-check

运行：

1． 运行Selenium Grid

$ ant launch-hub

通过浏览器访问Selenium Grid控制台验证Selenium Grid启动成功：

http://localhost:4444/console

2． 运行Selenium Remote Control

与Grid在同一台机器上,只要指定不同的端口号，就可以运行多个RC在同一台机器上：

	$ant -Dport=5556 launch-remote-control

	$ant -Dport=5557 launch-remote-control

	$ant -Dport=5558 launch-remote-control

运行后，通过访问Grid控制台，验证RC注册成功：

<table class="MsoNormalTable" style="MARGIN: auto auto auto 43.3pt; mso-cellspacing: 1.5pt; mso-yfti-tbllook: 1184" cellpadding="0" border="1">
<tbody>
<tr style="mso-yfti-irow: 0; mso-yfti-firstrow: yes">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">localhost</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">5555</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">*firefox</span></p>
</td>
</tr>
<tr style="mso-yfti-irow: 1">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">&nbsp;</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">&nbsp;</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">&nbsp;</td>
</tr>
<tr style="mso-yfti-irow: 2">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">localhost</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">5556</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">*firefox</span></p>
</td>
</tr>
<tr style="mso-yfti-irow: 3">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">localhost</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">5557</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">*firefox</span></p>
</td>
</tr>
<tr style="mso-yfti-irow: 4; mso-yfti-lastrow: yes">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">localhost</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">5558</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="font-size: 11pt; font-family: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt;" lang="EN-US">*firefox</span></p>
</td>
</tr>
</tbody>
</table>

与Gird不在同一台机器上，假设环境如下图：

<img src="/media/img/selenium-grid-2.jpg">

我们三台机器，一台机器hub.thoughtworks.com运行grid，其它两台rc1和rc2分别运行两个RC.

则我们需要在启动RC时，使用下列命令指定相应的参数：

$ant -Dport=<port> -Dhost=<hostname> -DhubURL=<hub url> -Denvironment="Firefox on Windows" launch-remote-control

其中，-Dport指定RC的端口号，-Dhost指定RC所在机器的Host名。-DhubURL指定Grid机器的URL，-Denvironment标示RC的浏览器和操作平台，具体可参考Grid控制台说明。

URL可以使Host Name，也可以是TCP/IP。

按照上图所示参数运行完毕后，通过Grid控制台验证成功结果如下：

<table class="MsoNormalTable" style="MARGIN: auto auto auto 41.25pt; mso-cellspacing: 1.5pt; mso-yfti-tbllook: 1184" cellpadding="0" border="1">
<tbody>
<tr style="mso-yfti-irow: 0; mso-yfti-firstrow: yes">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: center; mso-pagination: widow-orphan" align="center"><strong><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">Host</span></strong></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: center; mso-pagination: widow-orphan" align="center"><strong><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">Port</span></strong></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: center; mso-pagination: widow-orphan" align="center"><strong><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">Environment</span></strong></p>
</td>
</tr>
<tr style="mso-yfti-irow: 1">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">rc1.seleniumhq.org</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">5555</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">Firefox on Windows</span></p>
</td>
</tr>
<tr style="mso-yfti-irow: 2">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">rc1.seleniumhq.org</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">5556</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">Firefox on Windows</span></p>
</td>
</tr>
<tr style="mso-yfti-irow: 3">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">rc2.seleniumhq.org</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">5555</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">Firefox on Mac</span></p>
</td>
</tr>
<tr style="mso-yfti-irow: 4; mso-yfti-lastrow: yes">
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">rc2.seleniumhq.org</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">5556</span></p>
</td>
<td style="BORDER-RIGHT: #ece9d8; PADDING-RIGHT: 0.75pt; BORDER-TOP: #ece9d8; PADDING-LEFT: 0.75pt; PADDING-BOTTOM: 0.75pt; BORDER-LEFT: #ece9d8; PADDING-TOP: 0.75pt; BORDER-BOTTOM: #ece9d8; BACKGROUND-COLOR: transparent">
<p class="MsoNormal" style="MARGIN: 0cm 0cm 0pt; TEXT-ALIGN: left; mso-pagination: widow-orphan" align="left"><span style="FONT-SIZE: 11pt; FONT-FAMILY: 宋体; mso-bidi-font-family: 宋体; mso-font-kerning: 0pt" lang="EN-US">Firefox on Mac</span></p>
</td>
</tr>
</tbody>
</table>

3． 调用

下面是调用代码，运行的话需要写main函数或者与TestNG结合

{% highlight java %}

	[java] view plaincopy

    // seleniumHost Grid地址, seleniumPort Grid端口号, browser 浏览器, website 被测对象。  
         
    import static com.thoughtworks.selenium.grid.tools.ThreadSafeSeleniumSessionStorage.session;  
    import static com.thoughtworks.selenium.grid.tools.ThreadSafeSeleniumSessionStorage.startSeleniumSession;  
    import static org.testng.AssertJUnit.assertEquals;  
    import static org.testng.AssertJUnit.assertTrue;  
    import org.testng.annotations.AfterMethod;  
    import org.testng.annotations.BeforeMethod;  
    import org.testng.annotations.Parameters;  
      
      
      
    /** 
     * Base class for all tests in Selenium Grid Java examples. 
     */  
    public class FlickrTestBase {  
      
        public static final String TIMEOUT = "30000";  
      
        protected void startSession() throws Exception {  
            startSeleniumSession("localhost", 4444, "*iexplore", "http://www.google.cn");  
            session().setTimeout(TIMEOUT);  
        }  
      
        protected void closeSession() throws Exception {  
            closeSeleniumSession();  
        }  
      
        protected void runFlickrScenario(String searchString) {  
            session().open("/");  
            assertTrue(session().getLocation(), session().getLocation().startsWith("http://www.google.cn"));  
            session().type("q", searchString);  
            session().click("btnG");  
            session().waitForPageToLoad(TIMEOUT);  
            assertTrue(session().isTextPresent(searchString.split(" ")[0]));  
        }  
          
        public static void main(String[] args) throws Exception {  
      
            FlickrTestBase gt = new FlickrTestBase();  
            gt.startSession();  
            gt.runFlickrScenario("selenium");  
              
        }  
      
    }  

{% endhighlight %}

Selenium Grid FAQ:

<http://selenium-grid.seleniumhq.org/faq.html >