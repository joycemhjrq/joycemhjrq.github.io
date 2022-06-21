---
layout: post
title: loadrunner 常见问题
categories: loadrunner
tags: problem
---

## 1.LoadRunner录制脚本时为什么不弹出IE浏览器？ 

当一台主机上安装多个浏览器时，LoadRunner录制脚本经常遇到不能打开浏览器的情况，可以用下面的方法来解决。

    启动浏览器，打开Internet选项对话框，切换到高级标签，去掉“启用第三方浏览器扩展（需要重启动）”的勾选，然后再次运行VuGen即可解决问题
    提示：通常安装Firefox等浏览器后，都会勾选上面得选项，导致不能正常录制。因此建议运行LoadRunner得主机上保持一个干净的测试环境。

## 2.录制Web脚本时，生成的脚本中存在乱码该如何解决？

    录制脚本前，打开录制选项配置对话框Record-Options，进入到Advanced标签，先勾选“Support charset”，然后选择中支持UTF-8。再次录制，就不会出现中文乱码问题了。

## 3.HTML-based script与URL-based script的脚本有什么区别？

    使用“HTML-based script”的模式录制脚本，VuGen为用户的每个HTML操作生成单独的步骤，这种脚本看上去比较直观；使用“URL-based script”模式录制脚本时，VuGen可以捕获所有作为用户操作结果而发送到服务器的HTTP请求，然后为用户的每个请求分别生成对应方法。

    通常，基于浏览器的Web应用会使用“HTML-based script”模式来录制脚本；而没有基于浏览器的Web应用、Web应用中包含了与服务器进行交互的Java Applet、基于浏览器的应用中包含了向服务器进行通信的JavaScript/VBScript代码、基于浏览器的应用中使用了HTTPS安全协议，这时使用“URL-based script”模式进行录制。

## 4.为什么脚本中添加了检查方法Web-find，但是脚本回放时却没有执行？

    由于检查点功能会耗费一定的资源，因此LoadRunner默认关闭了对文本及图像的检查。要想开启检查功能，必须修改运行时的配置Run-time Setting。

    进入“Run-time Setting”对话框，依次进入“Internet Protocol→Preferences”，勾选Checks下的“Enable Image and text check”选项即可。

    检查执行结果时推荐使用web_reg_find方法。

## 5.运行时的Pacing设置主要影响什么？

    Pacing主要用来设置重复迭代脚本的间隔时间。共有三种方法：上次迭代结束后立刻开始、上次迭代结束后等待固定时间、按固定或随机的时间间隔开始执行新的迭代。
    根据实际需要设置迭代即可。通常，没有时间间隔会产生更大的压力。

## 6.运行时设置Log标签中，如果没有勾选“Enable logging”，则手工消息可以发送吗？

    Enable logging选项仅影响自动日志记录和通过lr_log_message发送的消息。即使没有勾选，虚拟用户脚本中如果使用lr_message、lr_output_message、lr_error_message,仍然会记录其发出的消息。

## 7.LoadRunner 8.0版本的VuGen在录制Web Services协议的脚本时一切正常，而回放时报出错误提示“Error：server returned an incorrectly formatted SOAP response”。这时说明原因引起的？

造成这种情况的主要原因是LoadRunner 8.0的VuGen在录制Web Service协议的脚本时存在一个缺陷：如果服务器的操作系统是中文的，VuGen会自动将WSDL文件的头改为，因此会有上面的错误提示。

    解决方法：把“LR80WebservicesFPI_setup.exe”和“lrunner_web_sevices_path_1.exe”两个补丁打上即可解决。

## 8.VuGen支持Netscape的客户证书吗？

    不支持。目前的VuGen 8.0版本中仅支持Internet Explorer的客户端证书。录制脚本时可以先从Netscape中导出所需的证书，然后将其导入到Internet Explorer中，并确保以相同的顺序导出和导入这些证书。而且，在每台将要录制或运行需要证书的Web Vuser脚本的计算机上都要重复执行前面的过程。

## 9.VuGen会修改录制浏览器中的代理服务器设置吗？

    会修改。在开始录制基于浏览器的Web user脚本时，VuGen首先会启动指定的浏览器。然后，VuGen会指示浏览器访问VuGen代理服务器。为此，VuGen会修改录制浏览器上的代 理服务器设置。默认情况下，VuGen会立即将代理服务器设置更改为Localhost:7777。录制之后，VuGen会将原始代理服务器设置还原到该录制浏览器中。因此，在VuGen进行录制的过程中，不可以更改代理服务器设置，否则将无法正常进行。

## 10.在LoadRunner脚本如何输出当前系统时间？

LoadRunner提供了char *ctime(const time_t *time)函数，调用参数为一个Long型的整数指针，用于存放返回时间的数值表示。

调用语句与返回值如下示例：

    typedef long time_t;
    Action()
    {
        time_t t;
        lr_message(“Time in seconds since 1/1/70: %ld\n”,time(&t));
        lr_message(“System time and date: %s”,ctime(&t));
    } 

    输出结果为：
    Time in seconds since 1/1/70: 1185329968
    System time and date:Wed Jul 25 10:19:28 2007

## 11.一些Web虚拟用户脚本录制后立刻回放没有任何问题，但是当设置迭代次数大于1时，如果进行回放则只能成功迭代一次。为什么从第二次迭代开始发生错误？

    这种现象多是由于在“Run-time Setting”的“Browse Emulation”的设置中，勾选了“Simulate a new user on each iteration”及其下面的选项“Clear cache on each iteration”这两个选项的含义是每次迭代时模拟一个新的用户及每次迭代时清除缓存。

    由于脚本迭代时，init和end只能执行一次，如果每次迭代都模拟一个新的用户并清除缓存，则用户登录信息将一并清除，因此迭代时可能会发生错误。

## 12.虚拟客户脚本“Run-time Setting”中的线程和进程运行方式的区别？

    如果选择“Run Vuser as a process”，则场景运行时会为每一个虚拟用户创建一个进程；选择“Run Vuser as a thread”则将每个虚拟用户作为一个线程来运行，在任务管理器中只看到一个mmdrv.exe，这种方式的运行效率更高，能造成更大的压力，时默认选 项。

    另外，如果启用了IP欺骗功能，则先在Controller中选中Tools菜单下的“Expert Mode”，然后将Tools菜单下的“Options>General”标签页中的IP地址分配方式也设置为与Vuser运行方式一致，同为线程或进程方式。

## 13.在Controller中运行Web相关测试场景时，经常会有很多超时错误提示，

如何处理这类问题？

    这主要有脚本的默认超时设置引起。当回放Web脚本时，有时候由于服务器响应时间较长，会产生超时的错误。这时需要修改脚本的运行时配置。

    进入“Run-time Setting”对话框后，依次进入“Internet Protocol→Preference”。然后点击“Options…”按钮，进入高级设置对话框，可以修改各类超时设置的默认值。

## 14.为什么Windows系统中的CPU、内存等资源仍然充足，但是模拟的用户数量却上不去？

在Windows计算机的标准设置下，操作系统的默认限制只能使用几百个Vuser，这个限制与CPU或内存无关，主要是操作系统本身规定了默认的最大线程数所导致。要想突破Windows这个限制，须修改Windows注册表。以Windows XP Professional为例。

    （1）打开注册表后，进入注册表项HKEY_LOCAL_MACHINE中的下列关键字：System\CurrentControlSet\Control\Session Manager\SubSystems。

    （2）找到Windows关键字，Windows关键字如下所示：

    　　%SystemRoot%\system32\csrss.exe bjectDirectory=\Windows

    　　SharedSection=1024,3072,512 Windows=On SubSystemType=Windows ServerDll=basesrv,1

    　　ServerDll=winsrv:UserServerDllInitialization,3 ServerDll=winsrv:ConServerDllInitialization,2

    　　ProfileControl=Off MaxRequestThreads=16

    　　SharedSection=1024,3072,512关键字的格式为xxxx,yyyy,zzz。其中，xxxx定义了系统范围堆的最大值（以KB为单位），yyyy定义每个桌面堆得大小。

    （3）将yyyy的设置从3072更改为8192（即8MB），增加SharedSection参数值。

通过对注册表的更改，系统将允许运行更多的线程，因而可以在计算机上运行更多的Vuser。这意味着能够模拟的最大并发用户数量将不受Windows操作系统的限制，而只受硬件和内部可伸缩性限制的约束。

## 15.Controller中设置了用户并发数量，但是运行时为何初始化的用户数量少于实际数量？

    主要时设置问题。在Tools→options→Run-time-setting中可以设置每次最多初始化的虚拟用户。如果需要100个并发用户，则将该值设置为大于100的数值。另外，注意LoadRunner相关协议License的更新，确保使用的License能够允许所需要的并发用户数量。

## 16.如何让场景的用户执行发生错误继续运行，以保证不间断进行压力测试？

    用VuGen打开虚拟用户脚本后，进入“Run-time Settings”对话框后，依次进入“General→Miscellaneous”，可以看到Miscellaneous设置中关于“Error Handling”的配置。勾选“Continue on error”即可让虚拟用户发生错误继续运行。

## 17.为什么.NET虚拟用户有时不能在远程主机执行？

    主要时LoadRunner的版本问题。根据笔者的经验，如果是Microsoft Visual Studio 2005开发的虚拟用户，同时LoadRunner客户端的版本低于8.1，执行Controller的主机将会发生错误。

因此要想正确的运行Microsoft Visual Studio 2005开发的.NET虚拟用户，客户端最好装8.1以上的版本，Controller的主机则安装8.0和8.1两个版本均可。此 外，产生压力的LoadRunner客户端上预先应该安装.NET运行环境，如果Microsoft Visual Studio 2005开发的是.NET虚拟用户，则应该安装Microsoft .NET Framework SDK v2.0。

## 18.测试分析结果中会统计Action时间，而实际上可能并不须要这些数据，如何只显示自己定义的用户事务？

    进入脚本的运行时设置，依次进入General→Miscellaneous。默认情况下，自动事务配置“Automatic Transactions”下有两个选项：第一个是把脚本的Action部分定义为一个事务；第二个时把脚本的每一部分定义为一个事务。去掉这两个勾选后，测试结果将会只显示自己定义的用户事务。

## 19.测试结果中，Summary和平均事务响应时间图里的各个事务的最大值、平均值、最小值为什么显示不一样？

主要是受采样时间的影响。Summary里的事务平均响应时间是根据整个场景执行过程得到的数据计算所得，最大值与最小值也是从整个场景中得到的。平均事务响应时间图主要时按照LoadRunner分析出来的采样频率来获取事务响应时间的最大值与最 小值，然后计算平均值。

    可以通过“Set Granularity”来修改平均事务响应时间图的采样频率。如果把“Granularity”设为场景执行时间，则统计结果将会一致。

## 20.统计结果中的总点击量Total Hits时用户的鼠标点击次数吗？

    Total Hits不时按照用户的鼠标点击次数来计算的，而是按照各个虚拟客户端向后台发起的总的请求数来进行统计的。例如在向服务器请求的一个页面中，如果该页面 包含5个图片，用户只要单击鼠标就可以访问该页面，而单个虚拟用户在LoadRunner访问的点击量为1+5=6次。

## 21.有些Web测试结果分析图（例如每秒返回页面数）在测试结果分析图中无法看到，

如何进行配置？

    用VuGen打开虚拟用户脚本后，进入“Run-time Settings”对话框后，依次进入“Internet Protocol>Preference”，可以看到一些Web性能图配置。
    勾选上面得选项后，Controller将会在测试执行过程中生成数据，然后可在Analysis中查看相应的性能结果分析图.

## 22. Error -27728: Step download timeout

loadruner报错：Error -27728: Step download timeout (120 seconds) 如何解决

错误分析：对于HTTP协议，默认的超时时间是120s（可以在LoadRunner中修改），如果超过这个时间，就会出现超时错误。

解决办法：对超时进行设置，默认的超时时间可以设置长一些（1、使用web_set_timeout 函数；2、“Runtime Setting”>“Internet Protocol:Preferences”>“Advanced”区域中点Options设置timeout时间长点；3、使用 lrs_set_recv_timeout函数），再设置多次迭代，如果还有超时现象，需要在“Runtime Setting”>“Internet Protocol:Preferences”>“Advanced”区域中勾选“winlnet replay instad of sockets”选项，再回放是否成功。

kernzhang解释如下（这里谢谢kernzhang，欢迎访问他的论坛：http://www.kernzhang.com)：

这个问题很有意思！呵呵!首先LR是通过Microsoft WinInet DLL去录制web协议的！但是在Control运行的时候它默认通过socket去模拟请求，因为这些可以真实的模拟带宽，而采用Microsoft WinInet DLL通过这个DLL去访问网卡方式去模拟带宽，使得模拟不是很精确！而且也不支持unix的应用，但是使用这个确实有时无法处理winnet Dll的一些请求，我认为是它的一些BUG，比如说：回放时它会检查Content-Length，但是网页支持receive more data时，这时socket模拟会一直等待直到timeout!

先说了一些优缺点，最后回到这个问题！这个问题分两个方面分析：

    第一：你要明白web_set_timeout()这个函数的适用范围！比如说一个web_submit_data（）中实际涵盖了10个对Server 端的请求，这个函数是针对10个请求的总和时间的！（别犯低级错误，timeout分了connect,receive以及download三个部分：） ）
    第二：就是我解释的上面的一些BUG问题！

WinInet dll在新版本中处理请求时可以异步的，就是不再是那种连接等待然后超时模式！但是LR用的socket是同步请求！只有等到timeout才会退 出！microsoft已经明确表示INTERNET_OPTION_RECEIVE_TIMEOUT 不再适用于 Microsoft Internet Explorer 5.0，显而易见，他们处理请求采取了异步处理的方式！呵呵！这下大概可以圆满解释你的问题了！呵呵

这里，我补充如下：
VuGen专用的基于套接字的重播是一种可伸缩以便进行负载测试的轻型引擎。使用线程时是准确的。基于套接字的引擎不支持socks代理服务器。如果在这样的环境中录制，应该使用winInet重播引擎。

## 23. utf-8

loadrunner录制脚本的时候要根据它的Generation Log的页面源代码来选择录制语言，如果选择utf-8后录制出来的结果，Generation Log的页面源代码为乱码的话，就不要选择utf-8，再录制一次,否则你的断言是无法通过的。

如果选择utf-8后录制出来的结果，Generation Log的页面源代码不为乱码的话，就要选择utf-8. 

## 24. Controller cannot create Vusers

    1.Loadrunner agent 不启动。
    解决：开始-Lr-tools-lr-agent run-time setting进行设置。
    2.提示：Loadrunner Controller cannot create Vusers.
    Ensure that your load generators are available and that your scrīpt are valid。
    解决：打开：Virtual User Generator -tools - Create Controller Scenario 将 Load Generator 设置 为 localhost 就可以了。

当然需要在脚本打开的时候设置

—-其实本来Vuser G里就默认的是localhost了。出现此情况的原因可能是因为脚本是从别的机器拷过来的，不是本地创建的。如果直接在controller中加载，则会没法创建用户。

解决办法：如上。然后由vuser 里直接tools下create controller 就是在vuser脚本制作这边加载controller.而不是直接打开controller，再加载脚本。

## 25. 录制不到内容

今天使用LR录制WEb的时候发现录制不到内容，录制的时候有event  录制结束后action里为空，没录制到内容

试了好多种方法，还把LR重新安装都没有解决，最后在网上找一个方法：

把recording options 里的 port mapping  从 socket level 改成了 winlnet level data 就好了

不过不知道原因，反正就是好了，真是奇怪。

## 26. Error-27498

错误分析：这种错误常常是因为并发压力过大，服务器端太繁忙，无法及时响应客户端的请求而造成的，所以这个错误是正常现象，是压力过大造成的。

如果压力很小就出现这种错误，可能是脚本出错，要仔细检查，提示错误信息会定位某个具体问题发生的位置。

解决办法：例如上面的错误定位到某个URL上，需要再运行下场景，同时在其他机器上访问此URL。如果不能访问或时间过长，可能是服务器或者此应用不能支撑如此之大的负载。分析一下服务器，最好对其性能进行优化。

如果再次运行还有超时现象，就要在各种图形中分析一下原因，例如可以查看是否服务器、DNS、网络等方面存在的问题。

最后，增加一下运行时的超时设置，在“Run-Time Settings”>“Internet Protocol:Preferences”中，单击“options”，增加“HTTP-request connect timeout”或者“HTTP-request receive”的值。

## 27. lr脚本中出现乱码

错误现象：某个链接或者图片名称为中文乱码，脚本运行无法通过

错误分析：脚本录制可能采用的是URL-based script方式，如果程序定义的字符集合采用的是国际标准，脚本就会出现乱码现象。

解决办法：重新录制脚本，在录制脚本前，打开录制选项配置对话框进行设置，在“Recording Options”的“Advanced”选项里先将“Surport Charset”选中，然后先中支持“UTF-8”的选项。

## 28. lr HTTP服务器状态代码：例如常见的页面-404错误提示、-500错误提示。

错误现象 1：-404 Not Found服务器没有找到与请求URL相符的资源，但还可以继续运行直到结束。

错误分析：此处与请求URL相符的资源在录制脚本时已经被提交过一次，回放时不可再重复提交同样的次源，而需要更改提交资源的内容，每次回放一次脚本都要改变提交的数据，保证模拟实际环境，造成一定的负载压力。

解决办法：在出现错误的位置进行脚本关联，在必要时插入相应的函数。

错误现象 2：-500 Internal Server Error 服务器内部错误，脚本运行停止。

错误分析：服务器碰到了意外情况，使其无法继续回应请求。

解决办法：出现此错误是到致命的，说明问题很严重，需要从问题的出现位置进行检查，此时需要此程序的开发人员配合来解决，而且产生的原因根据实际情况来定，测试人员无法单独解决问题，而且应该尽快解决，以便于后面的测试。

## 29. lr请求无法找到 Error-27979

错误现象：Error-27979

这时在tree view中看不到此组件的相关URL。

错误分析：所选择的录制脚本模式不正确，通常情况下，基于浏览的Web应用会使用“HTML-based script”模式来录制脚本；而没有基于浏览器的Web应用、Web应用中包含了与服务器进行交互的Java Applet、基于浏览器的应用中包含了向服务器进行通信的JavaScript/VB Script代码、基于流星器的应用中使用HTTPS安全协议，这时则使用“URL-based script”模式进行录制。

解决办法：在“Recording Options”的“Internet Protocol”的“Recording”中选择“Recording Level”为“HTML-based script”，选择“Script Type”为“A script containing explicit”。然后再选择“URL-based script”模式来录制脚本。

## 30. lr不执行检查方法：例如添加了Web_find,但是回放过程中没有执行。

错误分析：由于检查功能会消耗一定的资源，因此lr默认关闭了对文本以及图像的检查，所以在设置检查点后，需要开启检查功能。

解决办法：在“Run-time Settings”的“Internet Protocol”选项里的“Perference”中勾选“Check”下的“Enable Image and text check”选项。

## 31. lr回放Web Services协议脚本错误

错误现象：选用lr8.0版本来录制Web Services协议的脚本没有任何错误提示，回放脚本时会出现如下错误提示：Error:server returned an incorrectly formatted SOAP response.

错误分析：lr8.0在录制Web Services协议的脚本时存在一个缺陷：如果服务器的操作系统是中文的，VuGen会自动将WSDL文件的头改为<?xml version = "1.0" encoding = "zh_cn"?>，所以才会有此错误提示。

解决办法：下载两个补丁，分别为“LR80WebServicesFPI_setup.exe”和“Irunner_web_services_patch_1.exe”安装上即可。

## 32. Error -27728: Step download timeout (120 seconds)的解决方法

loadruner报错：Error -27728: Step download timeout (120 seconds) 如何解决

 语法 检查通过，但是在并发执行一个查询时候报错Action.c(16): Error -27728: Step download timeout (120 seconds) has expired when downloading non-resource(s)，请问有啥子解决方法，我使用web_set_timeout ，好象不起作用，直接在option中设置timeout时间为600，（单位应该是秒吧）还是没有起作用，结果都还是提示（120seconds），说 明还是以120秒来判断的；使用lrs_set_recv_timeout，语法检查不过，说明库函数里面没有这个函数。

尝试步骤：

设置超时时间到600秒，回放还是出错。

后来我设置了runt time setting中的internet protocol-preferences中的advaced区域有一个winlnet replay instead of sockets选项，选项后再回放就成功了。

kernzhang解释如下（这里谢谢kernzhang，欢迎访问他的论坛：http://www.kernzhang.com)：

这 个问题很有意思！呵呵!首先LR是通过Microsoft WinInet DLL去录制web协议的！但是在Control运行的时候它默认通过socket去模拟请求，因为这些可以真实的模拟带宽，而采用Microsoft WinInet DLL通过这个DLL去访问网卡方式去模拟带宽，使得模拟不是很精确！而且也不支持unix的应用，但是使用这个确实有时无法处理winnet Dll的一些请求，我认为是它的一些BUG，比如说：回放时它会检查Content-Length，但是网页支持receive more data时，这时socket模拟会一直等待直到timeout!

先说了一些优缺点，最后回到这个问题！这个问题分两个方面分析：

第 一：你要明白web_set_timeout()这个函数的适用范围！比如说一个web_submit_data（）中实际涵盖了10个对Server 端的请求，这个函数是针对10个请求的总和时间的！（别犯低级错误，timeout分了connect,receive以及download三个部分：） ）

第二：就是我解释的上面的一些BUG问题！
WinInet dll在新版本中处理请求时可以异步的，就是不再是那种连接等待然后超时模式！但是LR用的socket是同步请求！只有等到timeout才会退 出！microsoft已经明确表示INTERNET_OPTION_RECEIVE_TIMEOUT 不再适用于 Microsoft Internet Explorer 5.0，显而易见，他们处理请求采取了异步处理的方式！呵呵！这下大概可以圆满解释你的问题了！呵呵

这里，我补充如下：

VuGen专用的基于套接字的重播是一种可伸缩以便进行负载测试的轻型引擎。使用线程时是准确的。基于套接字的引擎不支持socks代理服务器。如果在这样的环境中录制，应该使用winInet重播引擎。 











