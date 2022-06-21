---
layout: post
title: junit RepeatedTest用法
categories: junit
tags: junit
---

## 设定一个方法重复运行多次方法：

1、首先要在测试类里定义它的构造函数：

{% highlight java %}

public class TestMyStack extends TestCase {
    private MyStack ms;

    public TestMyStack(String name) {
        super(name);
    }

{% endhighlight %}

然后TestSuite（可以集合运行多个测试类）里面调用：

{% highlight java %}

package com.junit.test;

import junit.extensions.RepeatedTest;
import junit.framework.Test;
import junit.framework.TestCase;
import junit.framework.TestSuite;

public class TestAll extends TestCase {

    public static Test suite() {
        TestSuite suite = new TestSuite();
        suite.addTestSuite(TestCalcular.class);
        suite.addTestSuite(TestMyStack.class);
       
        suite.addTest(new RepeatedTest(new TestMyStack("testPushNormal1"), 20));
//调用RepeatedTest，传入要重复运行的类的方法。
        return suite;
    }

}

{% endhighlight %}