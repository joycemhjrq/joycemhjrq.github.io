---
layout: post
title: Selenium 基于CORE/IDE的简单应用
categories: Selenium
tags: ide
---

<http://hi.baidu.com/suofang/blog/item/bb0b56af64e1bdeffaed5068.html>

## Selenium简介--总体介绍

上篇文章我们总体介绍了Selenium，在这篇文章里，让我们一起了解下Selenium基于CORE/IDE的简单应用。在开始前，我们先回顾下上篇文章中的内容：

Selenium Core是Selenium的核心部分,它由一些纯js代码组成， 可以运行在windows/linux的不同browser上, 而IDE是在core的基础上的一种应用, 通过UI实现类似QTP/Winner的脚本录制回放功能。

Core的运行模式主要有：

	1. Selenium IDE 运行
	利用IDE插件加载core引擎，运行测试脚本。主要用于脚本录制调试，类似于开发工具的调试，一般不应用于测试。
	2. 部署于被测对象服务器上，以http方式运行
	将core核心引擎和测试代码部署与被测对象的Web服务器上，以http请求的方式运行。
	3. 本地模式HTA and Chrome
	HTA模式是在IE下运行，Chrome是在Firefox下运行。这个两个的原理都是利用浏览器的本地化模式，调用主html文件--Test Runner 加载核心引擎库，执行test suite里的测试用例。

## Selenium IDE说明：

Selenium IDE是通过监听用户对html页面的操作来录制脚本的,是真正能够监听用户对html页面的操作的录制工具。 Selenium IDE可以生成7种语言的脚本：html,java,C#,ruby,python,perl,php。

你可以直接在FireFox中将Selenium IDE打开，执行html格式的脚本。 

如果你要执行其他语言格式的Selenium脚本，那么，你需要使用Selenium服务器，使用RC模式。6种语言的使用方法都是一样的。

在简单的回顾了上篇文章的内容后，让我们开始进入实践环节，自动化测试的关键，当然是要有测试脚本，Selenium core的测试脚本是html格式的，如过你很熟悉的话，可以自己手工编写测试脚本。当然，Selenium core也提供了脚本录制的工具—Selenium IDE。下面，我们就从脚本录制开始，使用Selenium core完成一个简单的测试脚本。

## 一、 准备工作：

1. Mozilla Firefox

下载地址：

http://www.mozillaonline.com/

安装后设置：

屏蔽‘阻止弹出窗口

<img src="/media/img/selenium-ide-1.jpg">

2. Selenium IDE

下载地址：

http://release.openqa.org/selenium-ide/1.0-beta-2/selenium-ide-1.0-beta-2.xpi

安装：

利用FireFox打开这个文件或者直接在FireFox中打开IDE链接后安装。

重新启动FireFox，打开‘工具’ 菜单，点击 Selenium IDE 后即可弹出IDE窗口。

3. Selenium Core

下载地址：

http://release.seleniumhq.org/selenium-core/1.0-beta-2/selenium-core-1.0-beta-2.zip
	　　
4. UltraEdit

推荐安装，用于编辑修改html运行脚本。

## 二、 录制脚本

准备工作做好后，让我们使用IDE来录制脚本。启动Firefox浏览器，在Firefox菜单栏中单击“工具”菜单，我们会看到Selenium IDE是其子菜单：

<img src="/media/img/selenium-ide-2.jpg">

单击Selenium IDE项我们可以看到弹出Selenium IDE窗口：

<img src="/media/img/selenium-ide-2.jpg"> 　　

然后我们就可以使用Selenium IDE进行录制了。切换的Firefox 浏览器，在地址栏输入www.google.cn，访问google。流程为：打开Google首页->在google搜索框中输入“Google 黑板报”->左键单击“google 搜索”按钮->在新页面选中“Google 中国的博客网志”，鼠标单击右键，在下列列表中选择verifyTextPresent项以验证搜索成功。如下图：
 
 <img src="/media/img/selenium-ide-3.jpg">

回到IDE界面，单击录制按钮停止录制。然后就可以单击播放按钮播放我们刚录制的脚本了，如下图：
 
<img src="/media/img/selenium-ide-4.jpg">

点击Source标签，我们可以看到如下的html格式脚本:

	<table cellpadding="1" cellspacing="1" border="1">
	<thead>
	<tr><td rowspan="1" colspan="3">New Test</td></tr>
	</thead><tbody>
	<tr>
	 <td>open</td>
	 <td>/</td>
	 <td></td>
	</tr>
	<tr>
	 <td>type</td>
	 <td>q</td>
	 <td>Google 黑板报</td>
	</tr>
	<tr>
	 <td>clickAndWait</td>
	 <td>btnG</td>
	 <td></td>
	</tr>
	<tr>
	 <td>verifyTextPresent</td>
	 <td>Google 中国的博客网志</td>
	 <td></td>
	</tr>
	</tbody></table>
 

　　如果你了解html，你就会看到，Selenium core的脚步命令式以table的格式编写的，一行(<tr>*</tr>)代表一个步骤。每行的第一列（<td>*</td>），是Command 或者 Action,指示Selenium要干什么。第二列是被操作目标，如要点击的button id，要输入的text id等。第三列是该动作（Command or action）需要的参数。如上面的html脚本在浏览器显示如下：

	New Test
	open /  
	type q Google 黑板报
	clickAndWait btnG  
	verifyTextPresent Google 中国的博客网志  

第一行的作用是open一个url, 因为在Selenium IDE里自动指定了baseURL，所以这里的被操作对象是一个”/”, 代表打开baseURL。如下图：

<img src="/media/img/selenium-ide-5.jpg">
 
因此，在编写脚本时，要注意给baseURL赋值，或者直接指定url。

第二行type命令，向id 是q的文本框输入“Google 黑板报”。

第三行clickAndWait命令,单击id是btnG的button，并等待页面提交成功。

第四行verifyTextPresent命令,在页面上验证有没有“Google 中国的博客网志”的文本。

另外需要说明的是，我们可以使用Selenium的菜单栏“Options”菜单中的“Format”子菜单将脚本转化为各自所需的语言类型。

## 三、 编辑部署脚本

在上次我们介绍了详细的Selenium Core脚本的录制与回放。在脚本编写好后，我们如何部署我们的脚步呢？这里有一个关键的概念—Test Suite,即，一些test case的集合。

你可以在Selenium core的tests目录下找到这个文件：TestSuite.html, 如下：

	Test Suite
	TestUseXpathLibrary
	TestMouseEvents

我们只要将Test Suite的连接，指向我们的test case就可以了。一个Test Suite，可以指向N个Test case。下面，我们就详细介绍各个步骤。

	1. 将我们编写好后的脚本保存在磁盘上。假定我们将其存放在D:\Selenium\TestCase目录下。文件名为testGoogle.html
	2. 将Selenium Core包里的core目录解压到D:\Selenium目录下。Selenium目录的结构如下：
	　　D:\Selenium
	　　        Core
	　　         TestCase
	3. 拷贝TestSuite.html到Test Case目录下，修改连接指向我们的Test case。
	Test Suite
	Test Google

这样，我们的脚本和Test Suite就准备好了，当然，这只是我们的学习用例，真正的测试基本的部署，需要考虑脚本的架构、模块的划分、后续的维护等等方面，在后续，我们有专门的章节来讨论。本章只是简单部署下，了解为主。

## 四、 本地模式运行测试脚本

在上次，我们简单介绍了Selenium的运行模式，我们的测试脚本和Test Suite已经完成，下面我们分别用三种模式，来运行我们的Test case。

1. HTA模式运行

HTA是HTML Application的缩写（HTML应用程序），直接将HTML保存成HTA的格式，就是一个独立的应用软件，这种模式可以让你避开浏览器安全的限制。当你需要在客户端创建被脚本标记为不安全的组件时，它就很有用处了；因此，用这种模式，可以避开安全错误以及一些错误弹出窗口。

因此，Selenium core模式下，我们可以直接通过hta模式来运行我们的测试脚本，具体步骤如下：

1.1 运行D:\Selenium\core目录下的TestRunner.hta，如下图:

<img src="/media/img/selenium-ide-7.jpg">

1.2 在Test Suite输入框里，输入Test Suite的地址，如下图：

<img src="/media/img/selenium-ide-8.jpg">

这里的地址是html的相对地址，当然，你也可以输入绝对地址，但不推荐这样做。

然后点击按钮“go”，就转到TestSuite页面了。

1.3 右侧操作栏有4个按钮，从左到右分别是“执行全部case、执行选中case、暂停和按步骤执行”，点击“执行全部case”，我们的测试用例就会被执行。

2. Chrome模式运行

在Mozilla下，通过Mozilla的chrome系统，安装于Mozilla的插件包可以用特殊的chrome URL关联访问。Chrome的全称是统一资源定位器。插件包安装过之后具有不受安全限制的优点，因此，和基于IE的hta模式具有同样优点，可以解决web测试中的跨站及安全限制问题。

用Chrome运行Selenium Core的步骤如下：

2.1 前提条件：安装了Mozilla Firefox，Mozilla Firefox安装了Selenium ide的插件包。

2.2 打开Firefox，输入如下url：

chrome://selenium-ide/content/selenium/TestRunner.html?test=file:///D:/selenium/TestCase/TestSuite.html&userExtensionsURL=&baseURL=http://www.google.cn

上面的url，第一处红色的部分是指定Test Suite的本地地址 ，第二处红色的部分，是指定本次测试的baseURL。

2.3 运行后，出现TestRunner页面，具体操作与1同。

3. 部署在被测目标服务器上

将Selenium core 和 测试用例与被测对象部署在同一个web服务器上，访问core目录下的TestRunner.html来测试被测对象。TestRunner.html和TestRunner.hta的界面操作相同。

## 五、 附Selenium Core命令指南

参考地址:<http://release.seleniumhq.org/selenium-core/1.0/reference.html>

## 一、  Commands (命令)

	Action
	对当前状态进行操作
	失败时，停止测试
	Assertion
	校验是否有产生正确的值
	Element Locators
	指定HTML中的某元素
	Patterns
	用于模式匹配
	1. Element Locators (元素定位器)

	id=id
	id locator 指定HTML中的唯一id的元素 
	 name=name
	name locator指定 HTML中相同name的元素中的第一个元素
	 identifier=id
	identifier locator 首先查找HTML是否存在该id的元素, 若不存在，查找第一个该name的元素 
	dom=javascriptExpression
	dom locator用JavaScript表达式来定位HTML中的元素,注意必须要以"document"开头
	例如:
	dom=document.forms['myForm'].myDropdown
	dom=document.images[56]
	 xpath=xpathExpression
	xpath locator用 XPath 表达式来定位HTML中的元素,必须注意要以"//"开头
	例如：
	xpath=//img[@alt='The image alt text']
	xpath=//table[@id='table1']//tr[4]/td[2]
	 link=textPattern
	link locator 用link来选择HTML中的连接或锚元素
	例如:
	link=The link text
	在没有locator前序的情况下 Without a locator prefix, Selenium uses:
	如果以"document."开头，则默认是使用 dom locator，如果是以"//"开头，则默认使用xpath locator,其余情况均认作identifier locator
	2. String Matching Patterns (字符串匹配模式)

	glob:patthern
	glob模式，用通配符"*"代表任意长度字符，"?"代表一个字符
	regexp:regexp
	正则表达式模式，用JavaScript正则表达式的形式匹配字符串
	exact:string
	精确匹配模式，精确匹配整个字符串，不能用通配符
	在没有指定字符串匹配前序的时候，selenium 默认使用golb 匹配模式
	3. Select Option Specifiers (Select选项指定器)

	label=labelPattern
	通过匹配选项中的文本指定选项
	例如：label=regexp:^[Oo]ther
	value=valuePattern
	通过匹配选项中的值指定选项
	例如：value=other
	id=id
	通过匹配选项的id指定选项
	例如: id=option1
	index=index
	通过匹配选项的序号指定选项，序号从0开始
	例如：index=2
	在没有选项选择前序的情况下，默认是匹配选项的文本

## 二、 Actions

	描述了用户所会作出的操作。
	Action 有两种形式: action和actionAndWait, action会立即执行，而actionAndWait会假设需要较长时间才能得到该action的相响，而作出等待，open则是会自动处理等待时间。

	click
	click(elementLocator)
	- 点击连接,按钮，复选和单选框
	- 如果点击后需要等待响应，则用"clickAndWait"
	- 如果是需要经过JavaScript的alert或confirm对话框后才能继续操作，则需要调用verify或assert来告诉Selenium你期望对对话框进行什么操作。 click  aCheckbox   
	clickAndWait  submitButton   
	clickAndWait  anyLink   

	open
	open(url)
	- 在浏览器中打开URL,可以接受相对和绝对路径两种形式
	- 注意：该URL必须在与浏览器相同的安全限定范围之内 open  /mypage   
	open  http://localhost/   

	type
	 type(inputLocator, value)
	- 模拟人手的输入过程，往指定的input中输入值
	- 也适合给复选和单选框赋值
	- 在这个例子中，则只是给钩选了的复选框赋值，注意，而不是改写其文本 type  nameField  John Smith 
	typeAndWait  textBoxThatSubmitsOnChange  newValue 

	select
	select(dropDownLocator, optionSpecifier)
	- 根据optionSpecifier选项选择器来选择一个下拉菜单选项
	- 如果有多于一个选择器的时候，如在用通配符模式，如"f*b*",或者超过一个选项有相同的文本或值，则会选择第一个匹配到的值 select   dropDown  Australian Dollars 
	select   dropDown  index=0 
	selectAndWait  currencySelector  value=AUD 
	selectAndWait  currencySelector  label=Auslian D*rs 

	 goBack,close
	goBack()
	模拟点击浏览器的后退按钮
	close()
	模拟点击浏览器关闭按钮
	selectWindow
	select(windowId)
	- 选择一个弹出窗口
	- 当选中那个窗口的时候，所有的命令将会转移到那窗口中执行 selectWindow  myPopupWindow   
	selectWindow  null   

	pause
	pause(millisenconds)
	- 根据指定时间暂停Selenium脚本执行
	- 常用在调试脚本或等待服务器段响应时 pause  5000   
	pause  2000   

	fireEvent
	 fireEvent(elementLocatore,evenName)
	模拟页面元素事件被激活的处理动作 fireEvent  textField  focus 
	fireEvent  dropDown  blur 

	waitForCondition
	waitForCondition(JavaScriptSnippet,time)
	- 在限定时间内，等待一段JavaScript代码返回true值，超时则停止等待 waitForCondition  var value=selenium.getText("foo"); value.match(/bar/);  3000 

	waitForValue
	waitForValue(inputLocator, value)
	- 等待某input(如hidden input)被赋予某值，
	- 会轮流检测该值，所以要注意如果该值长时间一直不赋予该input该值的话，可能会导致阻塞 waitForValue  finishIndication  isfinished 
	     
	store,stroreValue
	store(valueToStore, variablename)
	保存一个值到变量里。
	该值可以由自其他变量组合而成或通过JavaScript表达式赋值给变量 store  Mr John Smith  fullname 
	store  $.｛title｝ $.｛firstname｝ $.｛suname｝  fullname 
	store  javascript.｛Math.round(Math.PI*100)/100｝  PI 
	storeValue  inputLocator  variableName 

	把指定的input中的值保存到变量中

	storeValue  userName  userID 
	type  userName  $.｛userID｝ 

	storeText, storeAttribute
	storeText(elementLocator, variablename)
	把指定元素的文本值赋予给变量 storeText  currentDate  expectedStartDate 
	verifyValue  startDate  $.｛expectedStartDate｝ 

	storeAttribute(.｛｝.｛｝)
	把指定元素的属性的值赋予给变量

	storeAttribute     classOfInput1 
	verifyAttribute    $.｛classOfInput1｝ 

	chooseCancel.., answer..
	chooseCancelOnNextConfirmation()
	- 当下次JavaScript弹出confirm对话框的时候,让selenium选择Cancel
	- 如果没有该命令时，遇到confirm对话框Selenium默认返回true，如手动选择OK按钮一样 chooseCancelOnNextConfirmation      

	- 如果已经运行过该命令，当下一次又有confirm对话框出现时，也会同样地再次选择Cancel
	answerOnNextPrompt(answerString)
	- 在下次JavaScript弹出prompt提示框时，赋予其anweerString的值，并选择确定

	answerOnNextPrompt  Kangaroo   

## 三、 Assertions

	允许用户去检查当前状态。两种模式: Assert 和 Verify， 当Assert失败，则退出测试；当Verify失败，测试会继续运行。

	assertLocation, assertTitle
	assertLocation(relativeLocation)
	判断当前是在正确的页面 verifyLocation  /mypage   
	assertLocation  /mypage   

	assertTitle(titlePattern)
	检查当前页面的title是否正确 verifyTitle  My Page   
	assertTitle  My Page   

	assertValue
	assertValue(inputLocator, valuePattern)
	- 检查input的值
	- 对于 checkbox或radio，如果已选择，则值为"on",反之为"off" verifyValue  nameField  John Smith 
	assertValue  document.forms[2].nameField  John Smith 

	assertSelected, assertSelectedOptions
	assertSelected(selectLocator, optionSpecifier)
	检查select的下拉菜单中选中的选型是否和optionSpecifer(Select选择选项器)的选项相同 verifySelected  dropdown2  John Smith 
	verifySelected  dorpdown2  value=js*123 
	assertSelected  document.forms[2].dropDown  label=J*Smith 
	assertSelected  document.forms[2].dropDown  index=0 

	assertSelectOptions(selectLocator, optionLabelList)
	- 检查下拉菜单中的选项的文本是否和optionLabelList相同
	- optionLabelList是以逗号分割的一个字符串 verifySelectOptions  dropdown2  John Smith,Dave Bird 
	assertSelectOptions  document.forms[2].dropdown  Smith,J,Bird,D 

	assertText
	assertText(elementLocator,textPattern)
	- 检查指定元素的文本
	- 只对有包含文本的元素生效
	- 对于Mozilla类型的浏览器，用textContent取元素的文本，对于IE类型的浏览器，用innerText取元素文本 verifyText  statusMessage  Successful 
	assertText  //div[@id='foo']//h1  Successful 

	assertTextPresent, assertAttribute
	assertTextPresent(text)
	检查在当前给用户显示的页面上是否有出现指定的文本 verifyTextPresent  You are now logged in   
	assertTextPresent  You are now logged in   

	assertAttribute(.｛｝.｛｝, ValuePattern)
	检查当前指定元素的属性的值 verifyAttribute    bigAndBlod 
	assertAttribute  document.images[0]@alt  alt-text 
	verifyAttribute  //img[@id='foo']/alt  alt-text 

	assertTextPresent, etc.
	assertTextPresent(text)
	assertTextNotPresent(text)
	assertElementPresent(elementLocator)  verifyElementPresent    submitButton     
	 assertElementPresent    //img[@alt='foo']      assertElementNotPresent(elementLocator) 

	assertTable
	assertTable(cellAddress, valuePattern)
	- 检查table里的某个cell中的值
	- cellAddress的语法是tableName.row.column, 注意行列序号都是从0开始 verifyTable  myTable.1.6  Submitted 
	assertTable  results0.2  13 

	assertVisible, nonVisible
	assertVisible(elementLocator)
	- 检查指定的元素是否可视的
	- 隐藏一个元素可以用设置css的'visibility'属性为'hidden'，也可以设置'display'属性为'none' verfyVisible  postcode   
	assertVisible  postcode   

	assertNotVisible(elementLocator)  verfyNotVisible    postcode     
	 assertNotVisible    postcode     

	Editable, non-editable
	assertEditable(inputLocator)
	检查指定的input是否可以编辑 verifyEditable  shape   
	assertEditable  colour   

	assertNotEditable(inputLocator)
	检查指定的input是否不可以编辑
	assertAlert
	assertAlert(messagePattern)
	- 检查JavaScript是否有产生带指定message的alert对话框
	- alert产生的顺序必须与检查的顺序一致
	- 检查alert时会产生与手动点击'OK'按钮一样的效果。如果一个alert产生了，而你却没有去检查它，selenium会在下个action中报错。
	- 注意：Selenium 不支持 JavaScript 在onload()事件时 调用alert();在这种情况下，Selenium需要你自己手动来点击OK.
	assertConfirmation
	assertConfirmation(messagePattern)
	- 检查JavaScript是否有产生带指定message的confirmation对话框和alert情况一样，confirmation对话框也必须在它们产生的时候进行检查
	- 默认情况下，Selenium会让confirm() 返回true, 相当于手动点击Ok按钮的效果。你能够通过chooseCancelOnNextConfirmation命令让confirm()返回false.同样地，如果一个cofirmation对话框出现了，但你却没有检查的话，Selenium将会在下个action中报错
	- 注意：在Selenium的环境下，confirmation对话框框将不会再出现弹出显式对话框
	- 注意：Selenium不支持在onload()事件时调用confirmation对话框，在这种情况下，会出现显示confirmatioin对话框，并需要你自己手动点击。
	 assertPrompt
	assertPrompt(messagePattern)
	- 检查JavaScript是否有产生带指定message的Prompt对话框
	- 你检查的prompt的顺序Prompt对话框产生的顺序必须相同
	- 必须在verifyPrompt之前调用answerOnNextPrompt命令
	- 如果prompt对话框出现了但你却没有检查，则Selenium会在下个action中报错 answerOnNextPrompt  Joe   
	click  id=delegate   
	verifyPrompt  Delegate to who?   

## 四、 Parameters and Variables

	参数和变量的声明范围由简单的赋值到JavaScript表达式赋值。
	Store，storeValue 和storeText 为下次访问保存值。
	在Selenium内部是用一个叫storeVars的map来保存变量名。

	Variable Substitution 变量替换
	提供了一个简单的方法去访问变量,语法 $.｛xxx｝ store  Mr  title 
	storeValue  nameField  surname 
	store  $.｛title｝ $.｛suname｝  fullname 
	type  textElement  Full name is: $.｛fullname｝ 

	 JavaScript Evaluation JavaScript赋值
	你能用JavaScript来构建任何你所需要的值。
	这个参数是以javascript开头，语法是 javascript.｛'with a trailing'｝。
	可以通过JavaScript表达式给某元素赋值。 store  javascript.｛'merchant'+(new Date()).getTime()｝  merchantId 
	type  textElement  javascript.｛storedVars['merchantId'].toUpperCase()｝ 

	Generating Unique values 产生唯一值. 
	问题：你需要唯一的用户名
	解决办法: 基于时间来产生用户名，如'fred'+(new Date().getTime()) 