---
layout: post
title: Android Adb及其它常见问题
categories: Android
tags: adb
---

## Adb connection Error远程主机强迫关闭了一个现有的连接

本文主要解决eclipse连接android手机时adb connection error的问题——reset adb.
 
环境为真机测试，偶尔会报如下错误
Java代码  收藏代码

    [2012-04-24 20:41:34 - DeviceMonitor]Adb connection Error:远程主机强迫关闭了一个现有的连接。  
    [2012-04-24 20:41:36 - DeviceMonitor]Connection attempts: 1  
    [2012-04-24 20:41:38 - DeviceMonitor]Connection attempts: 2  
    [2012-04-24 20:41:40 - DeviceMonitor]Connection attempts: 3  
    [2012-04-24 20:41:42 - DeviceMonitor]Connection attempts: 4  
    [2012-04-24 20:41:44 - DeviceMonitor]Connection attempts: 5  
    [2012-04-24 20:41:46 - DeviceMonitor]Connection attempts: 6  
    [2012-04-24 20:41:48 - DeviceMonitor]Connection attempts: 7  
    [2012-04-24 20:41:50 - DeviceMonitor]Connection attempts: 8  
    [2012-04-24 20:41:52 - DeviceMonitor]Connection attempts: 9  
    [2012-04-24 20:41:54 - DeviceMonitor]Connection attempts: 10  
    [2012-04-24 20:41:56 - DeviceMonitor]Connection attempts: 11  
    [2012-04-24 20:44:06 - ddms]ADB rejected shell command (ls -l /): closed  
    [2012-04-24 20:44:11 - ddms]ADB rejected shell command (ls -l /): closed  

之前都是重启eclipse解决，但偶尔还解决不了。对于真机需要拔掉数据线，关闭eclipse重启，重新连接手机解决。 

 

但由于eclipse实在过于笨重，关闭重启时间过长。找到另外一种解决方法：

eclipse中视图模式选择DDMS(还有常见的java和debug视图)， 显示Devices窗口，若无可通过选择window->show view->Devices显示，再选择下拉箭头中的reset adb。

<img src="/media/img/android-adb.jpg">

此时eclipse会再自动重试一次，输入Connection attempts：1即表示成功啦

## adb server is out of date. killing...

1:今天调试android的时候发现一个诡异的问题

    [html] view plaincopy
    C:\Users\xxxx>adb start-server   
    adb server is out of date.  killing...   
    ADB server didn't ACK   
    * failed to start daemon *   

adb 不管执行 shell devices 还是logcat 都会报错 

    [html] view plaincopy
    adb server is out of date.  killing...   

究其源就是adb server没启动 

到stackoverflow上查了一下 经过分析整理如下：

    [html] view plaincopy
    C:\Users\xxxx>adb nodaemon server   
    cannot bind 'tcp:5037'   

原来adb server 端口绑定失败 

继续查看到底是哪个端口给占用了

    [html] view plaincopy
    C:\Users\xxxxxx>netstat -ano | findstr "5037"   
      TCP    127.0.0.1:5037         0.0.0.0:0              LISTENING       4236   
      TCP    127.0.0.1:5037         127.0.0.1:49422        ESTABLISHED     4236   
      TCP    127.0.0.1:49422        127.0.0.1:5037         ESTABLISHED     3840   
      
打开任务管理器kill掉4236 这个进程。ok

## android 使用 POI 操作 Excel 07 报找不到Class

Android项目特定结构：它的项目下有个libs，把所有的Jar包放到这个文件夹下，不用引入就可以了，它会自动加载，如果手动建个文件夹，名字不是libs，把jar包放入里面引用的话，Android项目会认不到包

## Failure [INSTALL_FAILED_OLDER_SDK]

原先在1.6rc1上写的程序，拿到1.5的SDK重新编译后却不能用

abd install bin/xxx.apk

安装上去，提示错误是：

Failure [INSTALL_FAILED_OLDER_SDK]
 
找了一下，是这个原因：

打开源码目录下的AndroidManifest.xml文件，然后注释掉或者删除掉这行：

android:minSdkVersion="9"

## java.lang.NoClassDefFoundError: com.jayway.android.robotium.solo.Solo

在eclipse中进行如下设置

"Properties > Java Build Path > Libraries", delete "Android Dependencies" in my test project. 

"Properties > Java Build Path > Libraries", delete two robotium jar and click "Add JARs" to re-import robotium jar in my test project. 

"Properties > Java Build Path > Order and Export", check two checkbok of robotium jar (and othera you want exported.) 
Clean my test project. 

## robotium+ant 编译问题

robotium+ant的编译问题
用ant运行robotium的时候一直有一个问题，就是测试代码的编译问题，为什么直接运行Android SDK tools ant build.xml自带的test的时候，它是运行未编译过的代码，也就是一直运行的是旧的代码，必需先在eclipse里面手动右键运行Android test后，再ant 运行 test 运行的才是最新的代码，试过ant 里面自带的compile,debug，然后再运行test,从bin目录中看到，运行compile或者debug后，测试代码确实也编译成功，但是运行test的时候运行的还是旧的代码。这个问题一直困扰了我好久，网上查了好入也没有找到问题的办法，今天早上心血来潮看了下eclipse手动运行测试的时候logcat，发现原来Android在运行测试代码的时候，在打apk包的时候，会把测试代码一起更新到模拟器上，所以我们在运行test之前还要先把新得到的apk安装到虚拟机器上，build.xml里面有自带的方法：installd，运行debug,然后installd，然后test，这个时候运行的就是最新的代码。
虽然是个很简单很傻X的问题，却花了我这么久的时间来解决。

ant debug: Builds the application and signs it with a debug key. 
ant installd:install the tests and apk against the instrumented code
ant run-tests: Runs tests from the package defined in test.package property

## Robotium 之activity跳转之后的组件识别问题

在利用Robotium做自动化测试的时候，碰到一个小问题。

有些应用，会有一些类似于button或者之类的组件，点击可以进入到其他的界面，然后从其他界面选择一个值，将该值带回到前面的界面。

这时，第一个界面之前的其他的组件就变化了。如果你在跳转到其他界面之前，初始化了一些组件，比如Button，EditText之类的，可能回到界面就不能在操作了。

最好的方法是不要依赖于某个实例本身，比如说 clickOnView(button);button为Button的instance.可以用clickOnButton（String buttonName）替换。

这个问题的root cause是因为，页面跳转涉及到activity的跳转。

所以可以用一些动态的方法获得需要的组件，比如直接在方法中使用 clickOnView(solo.getCurrentButtons.get(i));之类的。

## robotium一个类多个测试不能同时运行的问题

robotium里面一个类里多个测试（@Smoke）一起运行时，第一个运行完了，第二个会卡住，不会往下运行，研究了几天发现问题如下：

 同一个类的不同Smoke，它们的的setUp是同一个，也就是说它们的入口的Activity是同一个，那这样的话，当第一个Smoke运行后，如果页面进入到其它的Activity里面，第二个Smoke运行时发现现在模拟器的Activity并不是第一个Activity，所以会报错，
 解决办法 ：

 在tearDown方法里面，让solo回到初始的入口（Activity）后，再执行teardown的操作

 如初始入口是Index：
 
    @Override
     protected void tearDown() {
         solo.goBackToActivity("Index");
         try {
             solo.finalize();
         } catch (Throwable e) {
             e.printStackTrace();
         }
         getActivity().finish();
         try {
             super.tearDown();
         } catch (Exception e) {
             e.printStackTrace();
         }
     }

## Unable to resolve activity for: Intent错误的解决方法

今天我继续学习编写robotium的测试程序。目标是前些天在网上下来的android的一个扫雷源码（类名为test，包名为jim.test)。我的测试程序(类名为testTest,包名为jim.test.test)。

按照昨天的学习成果我很快就建立好了一个android test application，首先在代码里用solo.searchText("000")找下扫雷程序里的一个TextView控件的文本，只是这么一个简 单的代码却在执行时遇到了麻烦。问题是这样的：在我Run这个测试程序失败后，我发现提示信息Failure Trace里显示“Unable to resolve activity for: Intent”

这个错误我在上一个程序中就碰到过，只是没太在意。今天我特意上google上查了下这个错误信息（顺便BS下百度，搜这个居然什么都找不到），结果问题是出在了

    public testTest（）｛
     super（"com.example.android.notepad",test.class);
    }

这里面super的第一个参数我原本以为只是一个字符串，没什么实际用处，因此是直接复制的上一个程序的代码。现在才知道这个参数一定要跟本测试程序testTest的Mainfest.xml文件中的android:targetPackage 这个字段一致

    android:targetPackage="jim.test" android:name="android.test.InstrumentationTestRunner" />

事后想想其实很明显，第二个参数是被测试程序test.class，那第一个肯定是被测试程序的包名了。

果然修改之后再Run，一切正常。








