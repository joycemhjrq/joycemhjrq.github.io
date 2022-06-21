---
layout: post
title: loadrunner 让一种协议可以使用自定义的函数
categories: loadrunner
tags: function
---

如果我们自己开发了一个很有用的函数，比如专门从excel中读取测试数据，要想在各个类型的Vuser脚本中都能使用这个函数，怎么办呢？

 方法：全局加载DLL模式

 全局加载DLL，DLL加载成功之后，可以调用DLL中定义的任何函数，而无顺在脚本中声明

 操作方法：

 向mdrv.dat文件（Loadrunner/dat目录下）的相应部分中添加要加载的DLL列表，

 例如，要在NT平台上为Winsocket Vuser加载DLL，请向mdrv.dat文件中添加下列语句（黑体语句）

    [WinSock]
    ExtPriorityType=protocol
    WINNT_EXT_LIBS=wsrun32.dll
    WIN95_EXT_LIBS=wsrun32.dll
    LINUX_EXT_LIBS=liblrs.so
    SOLARIS_EXT_LIBS=liblrs.so
    HPUX_EXT_LIBS=liblrs.sl
    AIX_EXT_LIBS=liblrs.so
    LibCfgFunc=winsock_exten_conf
    UtilityExt=lrun_api
    ExtMessageQueue=0
    ExtCmdLineOverwrite=-WinInet No
    ExtCmdLineConc=-UsingWinInet No
    SecurityRequirementsFiles=winsock.asl
    SecurityMode=On

    WINNT_DDLS=user_d1ll.dll,user_d1l2.dll,.... .

然后我们在Winsock脚本中就可以直接使用user_d1l1和user_d1l2中定义的函数了

同理，我们在mdrv.dat文件的其它Vuser配置部分中加入同样的语句，可以达到相同的目的。
