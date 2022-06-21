---
layout: post
title: loadrunner 关联
categories: loadrunner
tags: correlation
---

## 如何在 LoadRunner 脚本中做关联 (Correlation)

当录制脚本时，VuGen会拦截client端（浏览器）与server端（网站服务器）之间的对话，并且通通记录下来，产生脚本。在VuGen的Recording Log中，您可以找到浏览器与服务器之间所有的对话，包含通讯内容、日期、时间、浏览器的请求、服务器的响应内容等等。脚本和Recording Log最大的差别在于，脚本只记录了client端要对server端所说的话，而Recording Log则是完整纪录二者的对话。

 当执行脚本时，您可以把VuGen想象成是一个演员，它伪装成浏览器，然后根据脚本，把当初真的浏览器所说过的话，再对网站伺服器重新说一遍，VuGen企图骗过服务器，让服务器以为它就是当初的浏览器，然后把网站内容传送给VuGen。所以纪录在脚本中要跟服务器所说的话，完全与当初录制时所说的一样，是写死的（hard-coded）。这样的作法在遇到有些比较聪明的服务器时，还是会失效。这时就需要透过「关联（correlation）」的做法来让VuGen可以再次成功地骗过服务器。

## 何谓关联（correlation）？

所谓的关联（correlation）就是把脚本中某些写死的（hard-coded）数据，转变成是撷取自服务器所送的、动态的、每次都不一样的数据。

举一个常见的例子，刚刚提到有些比较聪明的服务器，这些服务器在每个浏览器第一次跟它要数据时，都会在数据中夹带一个唯一的辨识码，接下来就会利用这个辨识码来辨识跟它要数据的是不是同一个浏览器。一般称这个辨识码为Session ID。对于每个新的交易，服务器都会产生新的Session ID给浏览器。这也就是为什么执行脚本会失败的原因，因为VuGen还是用旧的Session ID向服务器要数据，服务器会发现这个Session ID是失效的或是它根本不认识这个Session ID，当然就不会传送正确的网页数据给VuGen了。

下面的图示说明了这样的情形：

当录制脚本时，浏览器送出网页A的请求，服务器将网页A的内容传送给浏览器，并且夹带了一个ID=123的数据，当浏览器再送出网页B的情求时，这时就要用到ID=123的数据，服务器才会认为这是合法的请求，并且把网页B的内容送回给浏览器。

在执行脚本时会发生什么状况？浏览器再送出网页B的请求时，用的还是当初录制的ID=123的数据，而不是用服务器新给的ID=456，整个脚本的执行就会失败。

要对付这种服务器，我们必须想办法找出这个Session ID到底是什么、位于何处，然后把它撷取下来，放到某个参数中，并且取代掉脚本中有用到Session ID的部份，这样就可以成功骗过服务器，正确地完成整个交易了。

## 哪些错误代表着我应该做关联（correlation）？

假 如脚本需要关联（correlation），在还没做之前是不会执行通过的，也就是说会有错误讯息发生。不过，很不幸地，并没有任何特定的错误讯息是和关 联（correlation）有关系的。会出现什么错误讯息，与系统实做的错误处理机制有关。错误讯息有可能会提醒您要重新登入，但是也有可能直接就显示 HTTP 404的错误讯息。

## 要如何做关联（correlation）？

关联（correlation）函数

关联（correlation）会用到下列的函数：

web_reg_save_param：这是最新版，也是最常用来做关联（correlation）的函数。

语法：

web_reg_save_param ( “Parameter Name” , < list of Attributes >, LAST );

web_create_html_param、web_create_html_param_ex：这二个函数主要是保留作为向前兼容的目的的。建议使用 web_reg_save_param 函数。
 详细用法请参考使用手册。在VuGen中点选【Help】>【Function reference】>【Contexts】>【Web and Wireless Vuser Functions】>【Correlation Functions】。

## 如何找出要关联（correlation）数据

简单的说，每一次执行时都会变动的值，就有可能需要做关联（correlation）。

VuGen提供二种方式帮助您找出需要做关联（correlation）的值：

    1. 自动关联
    2. 手动关联

## 自动关联
VuGen内建自动关联引擎（auto-correlation engine），可以自动找出需要关联的值，并且自动使用关联函数建立关联。

自动关联提供下列二种机制：

&  Rules Correlation：在录制过程中VuGen会根据订定的规则，实时自动找出要关联的值。规则来源有两种：

o 内建（Built-in Correlation）：

VuGen 已经针对常用的一些应用系统，如AribaBuyer、BlueMartini、BroadVision、InterStage、mySAP、NetDynamics、Oracle、PeopleSoft、Siebel、SilverJRunner等，内建关联规则，这些应用系统可能会有一种以 上的关联规则。您可以在【Recording Options】>【Internet Protocol】>【Correlation】中启用关联规则，则当录制这些应用系统的脚本时，VuGen会在脚本中自动建立关联。

 您也可以在【Recording Options】>【Internet Protocol】>【Correlation】检视每个关联规则的定义。

o 使用者自订（User-defined Rules Correlation）：

 除了内建的关联规则之外，使用者也可以自订关联规则。您可以在【Recording Options】>【Internet Protocol】>【Correlation】建立新的关联规则。

&  Correlation Studio：有别于Rules Correlation，Correlation Studio则是在执行脚本后才会建立关联，也就是说当录制完脚本后，脚本至少须被执行过一次，Correlation Studio才会作用。Correlation Studio会尝试找出录制时与执行时，服务器响应内容的差异部分，藉以找出需要关联的数据，并建立关联。

Rule Correlation

请依照以下步骤使用Rule Correlation：

    一、启用auto-correlation
        1. 点选VuGen的【Tools】>【Recording Options】，开启【Recording Options】对话窗口，选取【Internet Protocol】>【Correlation】，勾选【Enable correlation during recording】，以启用自动关联。
        2. 假如录制的应用系统属于内建关联规则的系统，如AribaBuyer、BlueMartini、BroadVision、InterStage、 mySAP、NetDynamics、Oracle、PeopleSoft、Siebel、SilverJRunner等，请勾选相对应的应用系统。
        3. 或者也可以针对录制的应用系统加入新的关联规则，此即为使用者自订的关联规则。
        4. 设定当VuGen侦测到符合关联规则的数据时，要如何处理：
        &  【Issue a pop-up message and let me decide online】：跳出一个讯息对话窗口，询问您是否要建立关联。
        &  【Perform correlation in sceipt】：直接自动建立关联
    二、录制脚本
     开始录制脚本，在录制过程中，当VuGen侦测到符合关联规则的数据时，会依照设定建立关联，您会在脚本中看到类似以下的脚本，此为BroadVision应用系统建立关联的例子，在脚本批注部分可以看到关联前的数据为何。
    三、 执行脚本验证关联是OK的。
    Correlation Studio
    当录制的应用系统不属于VuGen预设支持的应用系统时，Rule Correlation可能既无法发挥作用，这时可以利用Correlation Studio来做关联。
    Correlation Studio会尝试找出录制时与执行时，服务器响应内容的差异部分，藉以找出需要关联的数据，并建立关联。
     使用Correlation Studio的步骤如下：
        1. 录制脚本并执行
        2. 执行完毕后，VuGen会跳出下面的【Scan Action for Correlation】窗口，询问您是否要扫描脚本并建立关联，按下【Yes】按钮。

        3. 扫描完后，可以在脚本下方的【Correlation Results】中看到扫描的结果。

    四、 检查一下扫瞄的结果后，选择要做关联的数据，然后按下【Correlate】按钮，一笔一笔做，或是按下【Correlate All】让VuGen一次就对所有的数据建立关联。
     注意：由于Correlation Studio会找出所有有变动的数据，但是并不是所有的数据都需要做关联，所以不建议您直接用【Correlate All】。
    五、 一般来说，您必须一直重复步骤1~4直到所有需要做关联的数据都找出来为止。因为有时前面的关联还没做好之前，将无法执行到后面需要做关联的部份。
     有可能有些需要做关联的动态数据，连Correlation Studio都无法侦测出来，这时您就需要自行做手动关联了。
     手动关联
     手动关联的执行过程大致如下：
        1. 使用相同的业务流程与数据，录制二份脚本
        2. 使用WinDiff工具协助找出需要关联的数据
        3. 使用web_reg_save_param函数手动建立关联
        4. 将脚本中有用到关联的数据，以参数取代

接下来将详细的说明如何执行每个步骤

使用相同的业务流程与数据，录制二份脚本

    1. 先录制一份脚本并存档。
    2. 依照相同的操作步骤与数据录制第二份脚本并存盘。注意，所有的步骤和输入的数据一定都要一样，这样才能找出由服务器端产生的动态数据。
     有时候会遇到真的无法使用相同的输入数据，那您也要记住您使用的输入数据，到时才能判断是您输入的数据，还是变动的数据。
     使用WinDiff工具协助找出需要关联的数据
    3. 在第二份脚本中，点选VuGen的【Tools】>【Compare with Vuser…】，并选择第一份脚本。
    4. 接着WinDiff会开启，同时显示二份脚本，并显示有差异的地方。WinDiff会以一整行黄色标示有差异的脚本，并且以红色的字体显示真正差异的文字。（假如没看到红色字体，请点选【Options】>【View】>【Show Inline Differences】）。
    5. 逐一检视二份脚本中差异的部份，每一个差异都可能是需要做关联的地方。选取差异的脚本，然后复制。
     在复制时，有时并不需要取整行脚本，可能只会选取脚本中的一部分。
     注意：请忽略lr_thik_time的差异部份，因为lr_thik_time是用来模拟每个步骤之间使用者思考延迟的时间。
    6. 接着要在Recording Log（单一protocol）或是Generation Log（多重protocol）中找这个值。将鼠标光标点到Recording Log的第一行开头，按下Ctrl+F，开启【Find】窗口，贴上刚刚复制的脚本，找出在Recording Log第一次出现的位置。

    结果会有二种：

    o 在Recording Log中找不到要找的数据，这时请先确认您找对了脚本，毕竟现在开启了二个几乎一样的脚本，很容易弄错。
    o 在Recording Log中找到了要找的数据，这时要确认数据是从服务器端传送过来的。首先可以先检查数据的标头，从标头的Receiving response可以知道数据是从服务器端传送到client端的。假如此数据第一次出现是在Sending request中，则表示此数据是由client端产生，不需要做关联，但是有可能需要做参数化（parameterized）。
     您要找的标头格式如下：
    *** [tid=b9 Action1 2] Receiving response from host astra.merc-int.com:80 ( 25/11/2002 12:04:00 )

     7. 现在您已经找到录制二次都不一样，而且是由服务器所产生的动态数据了，而此数据极有可能需要做关联。

## 手动关联

手动关联的过程大致如下：

    第一步：录制测试脚本，录制二遍
    第二步：使用WinDiff工具找出两次脚本的不同，判断是否需要进行关联
    第三步：确定插入关联的位置
    第四步：在VIEW TREE中使用web_reg_save_param函数手动建立关联
    第五步：将脚本中有用到关联的数据，用参数代替
    第六步：验证关联的正确性

下面详细介绍：

    第一步：录制测试脚本，录制二遍
        这一步就不用多说了，相同的操作，录制两份，分别保存
    第二步：使用WinDiff工具协助找出需要关联的数据
    　　1. 在第二份脚本中，点选VuGen的【Tools】>【Compare with Vuser…】，并选择第一份脚本。
    　 　2. 接着WinDiff会开启，同时显示二份脚本，并显示有差异的地方。WinDiff会以一整行黄色标示有差异的脚本，并且以红色的字体显示真正差异的文 字。（假如没看到红色字体，请点选【Options】>【View】>【Show Inline Differences】）。
    　　查看二份脚本中差异的部份，每一个差异都可能是需要做关联的地方。
    　　注意：lr_thik_time部分的差异可以忽略
    找到不同的部分后，复制，然后打开Recording Log或是Generation Log，按Ctrl+F，在查找窗口中粘贴差异部分的内容，点击查找找到后，查看该部分的信息，确认是客户端的请求信息还是服务器回应的信息

如果出现在$$$$$$ Request Header For Transaction With Id 3 Ended $$$$$$这个部分，那证明是客户端发出的请求，这里是不需要做关联的

一般做的关联都是出现在****** Response Header For Transaction With Id 7 ******和****** Response Body For Transaction With Id 7 ******中的部分。

在找到这个信息后，需要记录如下信息：

    a、记录这个不同数据之前的内容和之后的内容
    b、记录这个不同数据出现的位置，是Header还是Body

## 使用web_reg_save_param函数手动建立关联

在找到是由服务器所产生的动态数据之后，接下来要做的就是找出适当的位置，使用web_reg_save_param函数，将这个动态数据撷取到某个参数中。

    1. 要在哪里使用web_reg_save_param函数？

     在之前的步骤，我们已经在Execution Log找到可能需要关联的动态数据。在Execution Log中选取动态数据前的文字然后复制，我们将会利用这段文字，来帮助我们找出要关联的动态数据。

     不过在这之前我们要先找出使用web_reg_save_param函数的正确位置，所以我们要再重新执行一遍脚本，而且这次会开启所有的Log。

        1. 在VuGen中点选【Vuser】>【Run-Time Settings】。
        2. 点选【General】>【Log】。
        3. 勾选【Enable logging】、【Always sends messages】、【Extended log】，以及【Extended log】下的所有选项。
        4. 按下【OK】就可以执行脚本了。

    执行完脚本之后，在Execution Log中搜寻刚刚复制的字符串。找到字符串后，在字符串前面会有A.tion1.c(7)，这个7就是到时候要插入web_reg_save_param函数的位置，也就是要插入到脚本的第7行。

    在脚本的第7行前插入一行空白行，然后输入

    web_reg_save_param(“UserSession”,“UserSession” 这个 “UserSession” 就是到时要使用的参数名称，建议给个有意义的名字。

    注意：到这里整个web_reg_save_param函数还没完成。

    2. 找出web_reg_save_param中要用到的边界

    web_reg_save_param函数主要是透过动态数据的前面和后面的固定字符串，来辨识要撷取的动态数据的，所以我们还需要找出动态数据的边界字符串。
    找出左边界字符串
    再回到Execution Log中，选取动态数据前的字符串并且复制它。
    这时会有个问题，到底要选取多少字符串才足以唯一识别要找的动态数据呢？建议是越多越好，但是尽量不要包含到特殊字符。
    在这边我们选取「input type=hidden name=userSession value=」字符串。选好之后，还要再确认一次这段字符串真的是可以唯一识别的，所以我们在Execution Log中透过Ctrl+F的搜寻，找找看这段字符串是否可以找到要找的动态数据。假如找不到，web_reg_save_param函数还有个ORD参数可以使用，ORD参数可以设定出现在第几次的字符串才是要找的字符串。
    将这个边界字符串加到未完成的web_reg_save_param函数中：
    web_reg_save_param(“UserSession”, “LB= input type=hidden name=userSession value=”,
    找出右边界字符串
    接下来要找出动态数据的右边界字符串，这个字符串就比较好找了，从动态数据的最后一个字符开始，通常就是我们要找的右边界字符串了。
    以这个例子来看，就是「>」，所以再把右边界字符串加入，web_reg_save_param函数中，这时web_reg_save_param函数已经快完成了。最后再加上「LAST);」就完成整个web_reg_save_param函数了。
    web_reg_save_param(“UserSession”, “LB= input type=hidden name=userSession value=”, “RB=>”, LAST);

    将脚本中有用到关联的数据，以参数取代
    当使用web_reg_save_param建立参数后，接下来就是用“UserSession”参数去取代脚本中写死的（hard-coded）资料。

范例：

将“Name=userSession”, “Value=75893.0884568651DQADHfApHDHfcDtccpfAttcf”, ENDITEM,换成“Name=userSession”, “Value={UserSession}”, ENDITEM,

到这里您已经完成了一个关联了，接下来就是执行脚本，是否能成功运行，假如还是有问题，就要检查看看是否还需要再做另一个关联。

关于 `web_reg_save_param `函数对于关联(correlation)来说，`web_reg_save_param`是最重要的一个函数，其功能是在下载的网页内容中，透过设定的边界字符串，找出特定的数据并将其储存在一个参数中，以供后续脚本使用。接下来将针对`web_reg_save_param`做比较详细的说明。

Service and registration type function

web_reg_save_param是一个Service function。service function主要是用来完成一些特殊的工作的，如关联、设定proxy、提供认证信息等，当其作用时，不会对网页的内容做任何的修改。

`web_reg_save_param` 同时也是一个registration type function (只要函数名称中包含`_reg_`的字眼，表示其为registration type function)。registration type function意味着其真正作用的时机是在下一个action function完成时执行的。举例来说，当某个web_url执行时所接收到的网页内容中包含了要做关联的动态数据，则必须将 `web_reg_save_param放在此web_url`之前，则`web_reg_save_param`会在`web_url`执行完毕后，也就是网页内容 都下载完后，再执行`web_reg_save_param`找寻要做关联的动态数据并建立参数。

所以要记住一点，要使用registration type function时，要注意其放置的位置必须在要作用的action function之前。

语法int web_reg_save_param(const char *ParamName, <list of Attributes>, LAST);

参数说明

ParamName:存放动态数据的参数名称
list of Attributes:其它属性，包含 Notfound, LB, RB, RelFrameID, Search, ORD, SaveOffset, Convert, 以及 SaveLen。属性值不分大小写，例如Search=all。以下将详细说明每个属性值的意义:

    &  Notfound:指定当找不到要找的动态数据时该怎么处置。
    o Notfound=error:当找不到动态数据时，发出一个错误讯息。假如没设定此属性，此为LoadRunner的默认值。
    o Notfound=warning:当找不到动态数据时，不发出错误讯息，只发出警告，脚本也会继续执行下去不会中断。在对角本除错时，可以使用此属性值。
    &  LB:动态数据的左边界字符串。此属性质是必须要有的，而且区分大小写。
    &  RB:动态数据的右边界字符串。此属性质是必须要有的，而且区分大小写。
    &  RelFrameID:相对于URL而言，欲搜寻的网页的Frame。此属性质可以是All或是数字，而且可有可无。
    &  Search:搜寻的范围。可以是Headers(只搜寻headers)、Body(只搜寻body部分，不搜寻header)、 Noresource(只搜寻body部分，不搜寻header与resource)或是All(搜寻全部范围，此为默认值)。此属性质可有可无。
    &  ORD:指明从第几次出现的左边界开始才是要撷取的数据。此属性质可有可无，默认值是1。假如值为All，则所有找到符合的数据会储存在数组中。
    &  SaveOffset:当找到符合的动态数据时，从第几个字符开始才开始储存到参数中。此属性质不可为负数，其默认值为0。
    &  Convert:可能的值有二种:
    o HTML_TO_URL: 将HTML-encoded数据转成URL-encoded数据格式
    o HTML_TO_TEXT:将HTML-encoded数据转成纯文字数据格式
    &  SaveLen:从offect开始算起，到指定的长度内的字符串，才储存到参数中。此参数可有可无，默认值是-1，表示储存到结尾整个字符串。

范例

web_reg_save_param("A", "LB/ic=<a href=", "RB=&#39;>", "Ord=All", LAST);nner会搜寻网页中所有以 「<a href=」 开头，且以 「’>」结束，当中包含的字符串，并且储存在「A」参数中。

Tips and Tricks

以下提供一些关联的常见问题：

&  如何打印出参数值？

    lr_output_message这二个函数来做到。例如：
    lr_output_message(“Value Captured = %s”, lr_eval_string(“{ParameterName}”));
    lr_eval_string与lr_output_message函数的使用说明请参考LoadRunner Online Function Reference。

& 在脚本的data目录下找不到路制时的快照（snapshot）
 造成在脚本的data目录下找不到路制时的快照（snapshot）的可能原因如下：

    o 脚本是由VuGen 6.02或更早的版本所录制的
    o 汇入的Action不会包含快照（snapshot）的档案
    o 脚本是储存在只读的目录下，早成VuGen无法储存执行时撷取的快照（snapshot）
    o 某些步骤并不会产生快照（snapshot），如浏览某个资源
    o 快照（snapshot）功能被取消

【Tools】>【General options】>【Correlation】tab >【Save correlation information during replay】&  开启WinDiff时出现「File no longer available」的错误讯息

WinDiff这个工具有些限制，无法开启包含空格符的目录或是脚本，所以建议命名时不要使用空格符，并且尽可能将名称取短一点。

    &录制时突然跳出【Correlation warning】对话窗口
     当你有勾选自动关联的【Issue a popup message and let me decide online】选项，当VuGen发现有可能要做关联的数据时，就会跳出【Correlation warning】的窗口，询问你要做关联（Correlation in script）还是要忽略（Ignore）。
     另外你也可以勾选【Perform correlation in script】，让VuGen自动作关联，不会再跳出询问窗口。
     或是勾选【Disable correlation engine】，关闭自动关联的功能。

    &如何手动启动「Scan action for correlation」的功能
     要手动启动「Scan action for correlation」的功能，请先执行脚本一次后，点选【Vuser】>【Scan Action for Correlation】。

    &执行完脚本后并未出现【Scan Action for Correlation】窗口
     要启用【Scan Action for Correlation】功能，请点选【Tools】>【General options】>【Correlation】tab，勾选【Show Scan for correlation popup after replay of Vuser】选项。

## 关联补充

在脚本语言中，给定一个复杂的字符串，如果要取其中符合一定模式的字符或者字符串，就需要进行模式匹配，这个时候正则表达式就是最好的解决方案了。下面举个简单的例子说说。

给定一个字符串org：

“Value=/wEPDwUKLTY4MzkwMjI5MA9kFgJmD2QWAg==”, ENDITEM,

假如我们要匹配Value=/后面的这个很大的值，那么用正则表达式改怎么写呢？

下面给出一种Tcl语言的正则表达式写法(写法不止一种），

set org “Value=/wEPDwUKLTY4MzkwMjI5MA9kFgJmD2QWAg==”

if [regexp -all {Value=/(.*)==} $org match value] {
    puts $value
}

这里用到了两个参考，前面的边界和后面的边界，前面的边界是：\”Value=/, 后面的边界是：==\” 利用这两个边界，左边界和右边界，我们就可以准确的定位和匹配这个需要的字符串。

说了这么多，现在步入正题，LoadRunner中的关联。其实前面介绍了正则表达式，明白点的朋友可能已经知道我想说什么了–这就是我理解的LoadRunner中关联的内部实现，当然可能实际的实现比这个要复杂的多。

那个字符串org就是我从一段脚本中摘录过来的，是一个web page的源代码中的一句话，LoadRunner通过web_reg_save_param函数在内部利用正则表达式把这个值匹配下来，然后存起来，以备后面使用。

web_reg_save_param函数有两个很重要的参数，就是LB和RB，这就是我前面正则表达式里面用的左边界和右边界。这个值是在调用之前提前取出来的，所以`web_reg_save_param`这个方法一定要写在使用调用或者打开页面的前面。

log中输出参数：`lr_log_message("lt=%s",lr_eval_string("{lt}"))`;
