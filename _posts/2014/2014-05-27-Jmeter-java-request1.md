---
layout: post
title: jmeter Java Request1
categories: jmeter
tags: 
    - jmeter
    - java
---

## 1. 测试脚本的编写

其实这个就是使用jmeter 自己的测试框架，写一个测试用例，实现对被测对象的调用，然后打包即可。

### a、引入jmeter框架依赖

使用eclipse的时候新建工程，在工程上右键-》properties-》java build path
将jmeter/lib 下的包都进行引入。

### b、新建一个测试class JavaTest

### c、引入jmeter的框架

    import org.apache.jmeter.config.Arguments;
    import org.apache.jmeter.protocol.java.sampler.AbstractJavaSamplerClient;
    import org.apache.jmeter.protocol.java.sampler.JavaSamplerContext;
    import org.apache.jmeter.samplers.SampleResult;

### d、编写用例

{%highlight java %}

//新建的class必须继承"AbstractJavaSamplerClient"
 public class JavaTest extends AbstractJavaSamplerClient {
 //定义开始和截止的时间，可以在控制台打印出来时间，看响应情况
    private static long start = 0;
     private static long end = 0;
     Param param;
     IService service;
 //setup 开始
    public void setupTest(JavaSamplerContext arg0) {
         start = System.currentTimeMillis();
        
         try {
           
 //try中间是你进行调用的代码           
         } catch (Throwable e) {
             e.printStackTrace();
         }
     }
     @SuppressWarnings("unchecked")
     @Override
 //jmeter的测试框架
    public SampleResult runTest(JavaSamplerContext arg0) {
         SampleResult results = new SampleResult();
         try {  
             results.sampleStart();
           
 //对上面方法的调用，jmeter会对sampleStart和sampleEnd之间的行为进行计时，作为响应时间            
             results.sampleEnd();
             results.setSuccessful(true);
         } catch (Throwable e) {
            
             e.printStackTrace();
         }
       
         return results;
     }
     public void teardownTest(JavaSamplerContext arg0) {
         end = System.currentTimeMillis();
 //可以在调试的时候自己打印时间
        System.err.println("cost time:" + (end - start) / 1000);
     }
 //在eclipse中自行进行调试，新建一个main函数来完成运行
    public static void main(String[] args) {
         JavaSamplerContext arg0 = new JavaSamplerContext(new Arguments());
         PerformenceTest test = new JavaTest();
 //这三步可以很清晰的看出框架执行的方式       
         test.setupTest(arg0);
         test.runTest(arg0);
         test.teardownTest(arg0);
     }
 }

 {% endhighlight %}

## 2. jmeter脚本编写

使用jmeter ui客户端，在线程组下添加一个java request，将之前编写的用例放到jmeter的 lib/ext 目录下面，这个时候就可以在类名称下拉菜单中，选择到自己写的用例了。

## 3. 本地和远程调试

在本地调试的时候，将这个脚本依赖到的全部jar包都放到jmeter的 /lib 下面，只有自己实现的测试用例.jar 放到/lib/ext下面，这样jmeter的启动消耗会小一些；放到远程服务上进行压力的时候，和本地环境一样部署即可

写了一个demo，更直观一些

已给被测class为：

{% highlight java %}
public class HelloWorld {
     public static int sum = 0;
     public static void sum(int a, int b) {
         sum = a + b;
         // System.out.println(sum);
     }
     public static void main(String[] args) {
         // TODO Auto-generated method stub
         HelloWorld a = new HelloWorld();
         a.sum(1, 2);
     }
 }

{% endhighlight %}

测试上面这个class的性能，只需要实现一个用例，一直调用他，然后进行结果统计。测试case的实现：

{% highlight java %}

package TestCase;
 import org.apache.jmeter.config.Arguments;
 import org.apache.jmeter.protocol.java.sampler.AbstractJavaSamplerClient;
 import org.apache.jmeter.protocol.java.sampler.JavaSamplerContext;
 import org.apache.jmeter.samplers.SampleResult;
 //import jmeter 的相关外部依赖
import TestCase.HelloWorld;
 //import 被测对象
public class PerformenceTest extends AbstractJavaSamplerClient {
     private static long start = 0;
     private static long end = 0;
     public void setupTest(JavaSamplerContext arg0) {
         start = System.currentTimeMillis();//获取当前系统时间
        // System.out.println(start);
     }
     public SampleResult runTest(JavaSamplerContext arg0) {
         SampleResult sr = new SampleResult();
         sr.sampleStart();// jmeter 开始统计响应时间标记
        try {
             HelloWorld test = new HelloWorld();
             test.sum(1, 1);
 //被测对象调用
        } catch (Throwable e) {
             sr.setSuccessful(false);
         } finally {
             sr.sampleEnd();//jmeter 结束统计响应时间标记
        }
         return sr;
     }
     public void teardownTest(JavaSamplerContext arg0) {
         end = System.currentTimeMillis();
         // System.out.println(end);
         // System.out.println("The cost is"+(end-start)/1000);
     }
 //测试本地调试
    public static void main(String[] args) {
         // TODO Auto-generated method stub
         JavaSamplerContext arg0 = new JavaSamplerContext(new Arguments());
         PerformenceTest test = new PerformenceTest();
         test.setupTest(arg0);
         test.runTest(arg0);
         test.teardownTest(arg0);
     }
 }

{% endhighlight %}

有了上面的case，打包将jar放到jmeter/lib/ext中，可以在新建的jmeter脚本中获取测试方法，然后配置并发和启动延时，就可以了

## 经验：

这个例子本地拿来试了好久一直报错，在查看结果树的取样器结果里的Parsed视图中看到error count 一直为1，找了好久才定位到问题，原来是因为runTest方法里面的sr在运行没有抛异常的时候，没有把它的 sr.setSuccessful(true)这个方法置为true,这么看来，这个方法的默认值为false，所以要记得把正确运行情况下，把这个值置为true。否则找这个简单的一个问题都能找到吐血

还有，HelloWorld和PerformanceTese两个项目里面的main方法，只是本地调试用的，打jar包的时候可以去掉，Jmeter运行的是runTest里面的方法，setupTest是初始化，teardownTest是结束

打jar包，eclipse里面java项目右键Export-->Java-->JAR file，选择项目，输入存放地址，完成就行了。

## 具体步聚：

    1、eclipse新建项目
    2、新建lib包，引入jmeter的所有jar包，包括lib ext下面的jar包
    3、新建类，先建为你要测试的类，编写代码；然后新建一个测试类如PerformanceTest继承AbstractJavaSamplerClient，这个类里面最基本有三个方法：setupTest,runTest,teardownTest，在runTest里面调用所要测试的类，完成脚本，两个类都可以本地调试（即本地main方法调试，查看是否有错误）
    4、Export项目，打jar包
    5、把jar包放到Jmeter的lib的ext下面
    6、打开jmeter,新建线程组，添加取样器：java 请求，选择我们刚打的jar包的那个类
    7、添加监听器，设置线程，进行性能测试
