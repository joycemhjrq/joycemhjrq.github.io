---
layout: post
title: junit TestSuite的使用
categories: junit
tags: testsuit
---

（个人总结：Junit3只能addTestSuite而且添加的类必顺继承TestCase，而继承TestCase的类又不能用@Test开头，它只执行以test开始的方法，很坑呀，而且@Before，和@After方法也不认，就是继承Assert的一些标签，它都不会认）

貌似目前项目用到的Eclipse中新建Test Suite的向导是针对Junit3的，而且网上很多资料介绍Test Suite也都是Junit3的，这里简要介绍一下Junit4中Test Suite的使用方法。

一种是类似Junit3的方法：

Java代码 

{% highlight java %}

public class TestSuite1 {  
     public static Test suite() {  
         TestSuite suite = new TestSuite("Test for package1");  
         suite.addTest(new JUnit4TestAdapter(Test1.class));  
         suite.addTest(new JUnit4TestAdapter(Test2.class));        
         suite.addTest(new JUnit4TestAdapter(Test3.class));  
  
         suite.addTest(new JUnit4TestAdapter(TestSuite2.class));  
         return suite;  
     }  
}

{% endhighlight %}  

Junit3中是直接suite.addTest(Test1.class)，Junit4中需要用JUnit4TestAdapter来转换一 下。TestSuite中也可以添加TestSuite，方法是一样的，即suite.addTest(new JUnit4TestAdapter(TestSuite2.class));

另一种是使用标注的方法：

Java代码 

{% highlight java %}

@RunWith(Suite.class)  
@Suite.SuiteClasses({    
     Test1.class,   
     Test2.class,  
     Test3.class,  
       
     TestSuite2.class  
})  
public class TestSuite1 {  
}  

{% endhighlight %} 

TestSuite的使用

您定义自己的TestCase，并使用TestRunner来运行测试，事实上TestRunner并不直接运行 TestCase上的单元方法，而是透过TestSuite，TestSuite可以将数个TestCase在一起，而让每个TestCase保持简单。

 来看看一个例子：
 
MathToolTest.java

{% highlight java %}

package onlyfun.caterpillar.test;

import onlyfun.caterpillar.MathTool;
import junit.framework.TestCase;

public class MathToolTest extends TestCase {
    public MathToolTest(String testMethod) {
        super(testMethod);
    }

    public void testGcd() {
        assertEquals(5, MathTool.gcd(10, 5));
    }

    public static void main(String[] args) {
        junit.textui.TestRunner.run(MathToolTest.class);
    }
}

{% endhighlight %} 

在这个例子中，您并没有看到任何的TestSuite，事实上，如果您没有提供任何的TestSuite，TestRunner会自己建立一个，然後这个 TestSuite会使用反射（reflection）自动找出testXXX()方法。

 如果您要自行生成TestSuite，则在继承TestCase之後，提供静态的（static）的suite()方法，例如：
 
    public static Test suite() { 
         return new TestSuite(MathTool.class); 
    } 

如果您没有提供任何的TestSuite，则TestRunner就会像上面这样自动为您建立一个，并找出testXXX()方法，您也可以如下面定义 suite()方法：
 
    public static Test suite() { 
         TestSuite suite = new TestSuite(MathTool.class); 
         suite.addTest(new MathToolTest("testGcd")); 
         return suite; 
    } 
 
 JUnit 并没有规定您一定要使用testXXX()这样的方式来命名您的测试方法，如果您要提供自己的方法（当然JUnit 鼓励您使用testXXX()这样的方法名称），则可以如上撰写，为了要能够使用建构函式提供测试方法名称，您的TestCase必须提供如下的建构函 式：
 
    public MathToolTest(String testMethod) { 
        super(testMethod); 
    } 

如 果要加入更多的测试方法，使用addTest()就可以了，suite()方法传回一个TestSuite物件，它与 TestCase都实作了Test介面，TestRunner会调用TestSuite上的run()方法，然後TestSuite会将之委托给 TestCase上的run()方法，并执行每一个testXXX()方法。

除了组合TestCase之外，您还可以将数个TestSuite组合在一起，例如：
 
    public static Test suite() { 
        TestSuite suite= new TestSuite(); 
        suite.addTestSuite(TestCase1.class); 
        suite.addTestSuite(TestCase2.class); 
        return suite; 
    } 
 
如此之来，您可以一次运行所有的测试，而不必个别的运行每一个测试案例，您可以写一个运行全部测试的主测试，而在使用TestRunner时呼叫 suite()方法，例如：
 
    junit.textui.TestRunner.run(TestAll.suite()); 

TestCase与TestSuite都实作了Test介面，其运行方式为 Command 模式 的一个实例，而TestSuite可以组合数个TestSuite或TestCase，这是 Composite 模式 的一个实例。