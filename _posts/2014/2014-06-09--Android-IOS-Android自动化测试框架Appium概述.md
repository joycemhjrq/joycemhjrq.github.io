---
layout: post
title: Android IOS、Android自动化测试框架Appium概述
categories: Android
tags: 
    - ios
    - appium
---

[原文](http://www.testwo.com/article/56)

<img src="/media/img/android-appium-1.jpg">

## Appium

Appium是一个开源、跨平台的测试框架，可以用来测试原生及混合的移动端应用。Appium支持IOS、Android及FirefoxOS平台。 Appium使用WebDriver的json wire协议，来驱动Apple系统的UIAutomation库、Android系统的UIAutomator框架。Appium对IOS系统的支持得 益于Dan Cuellar’s对于IOS自动化的研究。Appium也集成了Selendroid，来支持老android版本。

使用Appium进行自动化测试有两个好处：

1. Appium在不同平台中使用了标准的自动化APIs，所以在跨平台时，不需要重新编译或者修改自己的应用。

2. Appium支持Selenium WebDriver支持的所有语言，如java、Object-C、JavaScript、Php、Python、Ruby、C#、Clojure，或者 Perl语言，更可以使用Selenium WebDriver的Api。Appium支持任何一种测试框架。如果只使用Apple的UIAutomation，我们只能用javascript来编 写测试用例，而且只能用Instruction来运行测试用例。同样，如果只使用Google的UIAutomation，我们就只能用java来编写测 试用例。Appium实现了真正的跨平台自动化测试。

（Appium使用selenium语言编写，selenium调用appium，appium检测当前系统已经连接的device(手机设备)，通过selenium语言调用appium驱动手机模拟器）

{% highlight java %}

package com.baidu.test;

import java.io.File;
import java.net.MalformedURLException;
import java.net.URL;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.openqa.selenium.By;
import org.openqa.selenium.Capabilities;
import org.openqa.selenium.HasTouchScreen;
import org.openqa.selenium.TouchScreen;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.remote.CapabilityType;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteTouchScreen;
import org.openqa.selenium.remote.RemoteWebDriver;

public class AndroidContactsTest {
   
    private WebDriver driver;
   
    @Before
    public void setUp() throws MalformedURLException {
       
        File app = new File("E:\\WorkSpaces\\Prometheus\\apk\\Prometheus.apk");
        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setCapability("device", "Android");
        capabilities.setCapability(CapabilityType.BROWSER_NAME, "");
        capabilities.setCapability(CapabilityType.VERSION, "2.2");
        capabilities.setCapability(CapabilityType.PLATFORM,"WINDOWS");
        capabilities.setCapability("app", app.getAbsolutePath());
        capabilities.setCapability("app-package", "com.zendaimoney.android.prometheus");
        capabilities.setCapability("app-activity", ".MainActivity");
       
        driver = new SwipeableWebDriver(new URL("http://127.0.0.1:4723/wd/hub"), capabilities); 


{% endhighlight %}

//调用appium，前提：

1、启动appium for windows

ip:0.0.0.0   ;    port:4723

2、启动模拟器：appium

3、Android SDK API >= 17 (Additional features require 18)

4、环境变量 ANDROID_HOME 并确保 $ANDROID_HOME/platform-tools 和 $ANDROID_HOME/tools 包含在 PATH 里

5、Make sure that hw.battery=yes in your AVD's config.ini. （比如我的 AVD 叫 appium， 所以我就要去编辑$Android_home/.android/avd/appium.avd/config.ini）(Mac 上只要连接上 USB 就可以了， 无需安装驱动。)    }

{% highlight java %}
   
    @After
    public void tearDown(){
        driver.quit();
    }
   
    @Test
    public void addContact(){
       
        WebElement el = driver.findElement(By.xpath("//TextView[2]"));
        el.click();
       
    }
   
    public class SwipeableWebDriver extends RemoteWebDriver implements HasTouchScreen{

        private RemoteTouchScreen touch;
       
        public SwipeableWebDriver(URL remoteAddress, Capabilities desiredCapabilities){
            super(remoteAddress, desiredCapabilities);
            touch = new RemoteTouchScreen(getExecuteMethod());
        }
       
        @Override
        public TouchScreen getTouch() {
            return touch;
        }}
}

{% endhighlight %}

Requirements

总体：

IOS自动化测试需要Mac os操作系统

Mac OS X 10.7或者更高版本，推荐10.8.4版本

Android自动化测试可以在Mac、Linux上进行。对于Windows平台的支持还在beta阶段

需要安装node和npm（node版本高于0.8）

IOS自动化：

Mac Xcode

Apple开发者工具（iphone模拟器sdk，及命令行工具）

Android自动化：

Android SDK API版本 >= 17,即android版本高于4.2

快速入门

方案1： 使用Appium.app

下载appium.app dmg

在Apple系统上安装appium.app，就可以直接运行自己的case

方案2： 使用node从命令行运行appium

安装node及npm

下面命令是在linux系统中安装appium

    mkdir appium-test && cd appium-test
    npm install -g appium  # might have to do this with sudo
    sudo authorize_ios # enable developer use of iOS sim
    npm install wd
    curl -O https://raw.github.com/appium/appium/master/sample-code/examples/node/simplest.js
    appium &
    node simplest.js

下面是一些不同语言编写的appium的测试用例

Example Tests: Node.js | Python | PHP | Ruby | Java

问题解决

如果使用过程遇到问题，可以参考这里，这里包含了一些常见错误的解决方法，也包含appium组织的联系方法。
使用appium编写测试用例

Appium支持Selenium WebDriver json wire 协议(部分支持，非全部）

appium定位元素的方法也和webdriver类似，但是也仅支持webdriver中的部分方法，详细信息见这里.

Appium也为json wire协议提供了一些拓展，来支持智能手机的手势动作，如tap（轻击）、flick，swipe，更多信息点击这里

Appium也支持在混合的app中，自动化测试web views，参见这里

Appium 同时支持Android和IOS平台：

[Appium自动化测试Android、IOS，环境搭建](https://github.com/appium/appium/blob/master/docs/running-on-osx.md#ios)

[Appium自动化测试Android，环境搭建](https://github.com/appium/appium/blob/master/docs/running-on-osx.md#android)

[Appium自动化测试Android，linux环境搭建](https://github.com/appium/appium/blob/master/docs/running-on-linux.md#android)

[Appium自动化测试IOS，目标app准备](https://github.com/appium/appium/blob/master/docs/running-tests.md#prep-ios)

[Appium自动化测试Android，目标app准备](https://github.com/appium/appium/blob/master/docs/running-tests.md#preparing-your-app-for-test-android)

[IOS自动化用例执行](https://github.com/appium/appium/blob/master/docs/running-tests.md#run-ios)

[Android自动化用例执行](https://github.com/appium/appium/blob/master/docs/running-tests.md#android-ios)

[在Mac系统中，Ruby使用appium进行自动化测试](https://github.com/appium/ruby_console/blob/master/osx.md)

Appium全部文档，点击[这里](https://github.com/appium/appium/blob/master/docs/)。