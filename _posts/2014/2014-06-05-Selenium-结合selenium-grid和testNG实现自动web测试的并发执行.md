---
layout: post
title: Selenium 结合selenium grid和testNG实现自动web测试的并发执行
categories: Selenium
tags: 
    - grid
    - testng
---

testNG可以设置为并发执行测试用例。selenium grid可以通过grid hub将测试用例分别转发给不同的remote control/浏览器对，而且这些remote control/浏览器对可以位于不同的机器上，这样两者结合就可以实现可伸缩的自动web测试。

1. testNG并发执行测试用例的方法

在配置testNG的test.xml中,通过suit标签的属性可以指定并发执行,例如:

<suite name="My suite" parallel="methods" thread-count="5">

指定每一个测试方法使用单独的线程,总线程数为5

parallel可以为：

    methods : 每个method使用一个线程
    tests ：每个<test>标签中的所有方法使用一个线程
    classes : 每个class使用一个线程

可以参看这里：<http://testng.org/doc/documentation-main.html#parallel-running>

如果是使用ant启动测试的话，还可以在ant脚本中指定该参数，可以参照selenium grid中build.xml的代码：
Xml代码

    <target name="run-demo-for-multiple-environments"  
            description="Run Selenium tests in parallel for multiple environments">  
      <java classpathref="demo.classpath" classname="org.testng.TestNG" failonerror="true" >  
    略】  
        <arg value="-d" />  
        <arg value="${basedir}/target/reports" />  
        <arg value="-suitename" />  
        <arg value="Selenium Grid Demo In Parallel For Multiple Environments" />  
     【在这里】  
        <arg value="-parallel"/>  
        <arg value="methods"/>  
        <arg value="-threadcount"/>  
        <arg value="10"/>  
        <arg value="-testclass"/>  
        <arg value="com.thoughtworks.selenium.grid.demo.WebTestInvolvingMultiEnvironments"/>  
      </java>  
    </target>  

2 使用selenium grid

grid的机制是，启动一个hub，然后启动多个remote control，启动remote control时告知hub的位置，这样这些rc就可以注册到hub上，测试程序与hub通讯，当测试被并发地发给hub时，hub会自动将这些测试命令 分发给已经注册的rc，rc接到命令后执行测试。

到这里D:/opensource/selenium/selenium-grid-1.0.4/doc/website/download.html下个最新版的selenium grid，里面的文档有详细的使用方法，现在简诉如下：

selenium grid要求安装jdk和ant，其目录下有一build.xml文件，其中定义的主要target如下：

检查配置：ant sanity-check

启动hub：ant launch-hub

其中hub的配置是在grid_configuration.yml文件里，该文件要在classpath的根里

启动后在http://host:port/console可以看到hub当前的状态

启动rc（按默认配置）：ant run-demo-in-sequence

启动rc（指定参数）：ant -Dport=5555 -Dhost=192.168.1.16 -DhubURL=http://192.168.1.1:4444 launch-remote-control

其中host和port是rc的地址，hubURL是hub的地址

正常启动后，可以在http://host:port/console看到hub中当前注册的rc

3 测试代码的写法

测试代码使用grid tool中
的 ThreadSafeSeleniumSessionStorage在每个用例前实例化一selenium（实际是一 DefaultSelenium），在用例后将其关闭。初始化selenium时与一般初始化DefaultSelenium一样，指定 host，port，browser和indexPage，这里的host和port不是rc中的server，而是hub。
以下是示例代码：

Java代码

{% highlight java %}

import static com.thoughtworks.selenium.grid.tools.ThreadSafeSeleniumSessionStorage.closeSeleniumSession;  
import static com.thoughtworks.selenium.grid.tools.ThreadSafeSeleniumSessionStorage.session;  
import static com.thoughtworks.selenium.grid.tools.ThreadSafeSeleniumSessionStorage.startSeleniumSession;  
import org.testng.annotations.AfterMethod;  
import org.testng.annotations.BeforeMethod;  
import org.testng.annotations.Parameters;  
import org.testng.annotations.Test;  
  
import com.thoughtworks.selenium.Selenium;  
  
  
public class Sample {  
    private Selenium selenium;  
      
    @BeforeMethod  
    @Parameters({"seleniumHost", "seleniumPort", "browser", "webSite"})  
    protected void startSession(String seleniumHost, int seleniumPort, String browser, String webSite) throws Exception {  
        startSeleniumSession(seleniumHost, seleniumPort, browser, webSite);  
        selenium = session();  
        selenium.setTimeout("120000");  
    }  
  
    @AfterMethod  
    protected void closeSession() throws Exception {  
        closeSeleniumSession();  
    }  
      
    @Test  
    public void test1() {  
        selenium.open("/");  
        selenium.type("q", "test1");  
        selenium.click("btnG");  
        selenium.waitForPageToLoad("30000");  
    }  
      
    【略】  
} 

{% endhighlight %}