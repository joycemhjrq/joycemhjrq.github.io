---
layout: post
title: java 利用htmlparser获取页面源代码
categories: java
tags: 
    - htmlparser
    - html
---
## java 利用htmlparser获取页面源代码:

>利用htmlparser获取页面源代码

{% highlight java %}

package com.ylzinfo.Result;
<<<<<<< HEAD:_posts/2014/2014-05-26-Java-htmlparser.md
import java.io.IOException;
import java.net.URL;
import java.net.URLConnection;
import org.htmlparser.Node;
import org.htmlparser.Parser;
import org.htmlparser.util.NodeIterator;
import org.htmlparser.util.ParserException;

public class GetFinalExcel {
    public static String path = "file:///E:/PerformanceTest/copy/5.html";
    public static String path1 = "http://www.baidu.com/";

    public static void main(String[] args) throws IOException, ParserException {
        Parser parser = new Parser((URLConnection) (new URL(path)).openConnection());
        
        for(NodeIterator i=parser.elements();i.hasMoreNodes();){
            Node node=i.nextNode();
            System.out.println(node.toHtml());
            System.err.println("mhj------------------>");
=======

    import java.io.IOException;
    import java.net.URL;
    import java.net.URLConnection;

    import org.htmlparser.Node;
    import org.htmlparser.Parser;
    import org.htmlparser.util.NodeIterator;
    import org.htmlparser.util.ParserException;

    public class GetFinalExcel {

        public static String path = "file:///E:/PerformanceTest/copy/5.html";
        public static String path1 = "http://www.baidu.com/";

        public static void main(String[] args) throws IOException, ParserException {
            Parser parser = new Parser((URLConnection) (new URL(path)).openConnection());
            
            for(NodeIterator i=parser.elements();i.hasMoreNodes();){
                Node node=i.nextNode();
                System.out.println(node.toHtml());
                System.err.println("mhj------------------>");
            }
>>>>>>> 9be93226d745358e6a013140656788a5df1dd7c8:_posts/2014/2014-05-26-Java-利用htmlparser获取页面源代码.md
        }
    }

{% endhighlight %}
