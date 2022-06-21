---
layout: post
title: Android 利用Hierarchy Viewer优化布局
categories: Android
tags: Viewer
---

<http://www.cnblogs.com/noTice520/　>

今天来讲下如何使用android中提供的工具优化我们的布局。首先我们写一个最简单的框架布局。

{% highlight xml %}

<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android" android:orientation="vertical" android:layout_width="fill_parent" android:layout_height="fill_parent">
    <TextView android:layout_width="300dip" android:layout_height="300dip" android:background="#00008B" android:layout_gravity="center" />
    <TextView android:layout_width="250dip" android:layout_height="250dip" android:background="#0000CD" android:layout_gravity="center" />
    <TextView android:layout_width="200dip" android:layout_height="200dip" android:background="#0000FF" android:layout_gravity="center" />
    <TextView android:layout_width="150dip" android:layout_height="150dip" android:background="#00BFFF" android:layout_gravity="center" />
    <TextView android:layout_width="100dip" android:layout_height="100dip" android:background="#00CED1" android:layout_gravity="center" />
</FrameLayout>

{% endhighlight %}

非常简单的一个布局，实现一个层叠的效果，运行效果如下图：

<img src="/media/img/android-view-1.jpg">

下面我们就用android中提供的一个观察布局的工具，层级观察器，Hierarchy Viewer来观察我们的布局。Hierarchy Viewer工具是一个非常好的布局优化工具，同时，你也可以通过它学习他人的布局。应该说是一个非常实用的工具。

Hierarchy Viewer在sdk的tools目录下，打开后最新界面如图所示：

<img src="/media/img/android-view-2.jpg">

界面很简洁，列出了当前设备上的进程，在前台的进程加粗显示。上面有三个选项，分别是刷新进程列表，将层次结构载入到树形图，截取屏幕到一个拥 有像素栅格的放大镜中。对应的在左下角可以进行三个视图的切换。在模拟器上打开写好的框架布局，在页面上选择，点击Load View，进入如图所示界面。

<img src="/media/img/android-view-3.jpg">

左边的大图为应用布局的树形结构，上面写有控件名称和id等信息，下方的圆形表示这个节点的渲染速度，从左至右分别为测量大小，布局和绘制。绿 色最快，红色最慢。右下角的数字为子节点在父节点中的索引，如果没有子节点则为0。点击可以查看对应控件预览图、该节点的子节点数（为6则有5个子节点） 以及具体渲染时间。双击可以打开控件图。右侧是树形结构的预览、控件属性和应用界面的结构预览。点击相应的树形图中的控件可以在右侧看到他在布局中的位置 和属性。工具栏有一系列的工具，保存为png或者psd，刷新等工具。其中有个load overlay选项可以加入新的图层。当你需要在你的布局中放上一个bitmap，你会用到它来帮你布局。点击左下角的第三个图标切换到像素视图，如下图 所示。

<img src="/media/img/android-view-4.jpg">

视图左侧为View和ViewGroup关系图，点击其中的View会在右边的图像中用红色线条为我们选中相应的View。最右侧为设备上的原图。中间为放大后带像素栅格的图像，可以在Zoom栏调整放大倍数。在这里能定位控件的坐标，颜色。观察布局就更加的方便了。

接下来再介绍下另一个布局优化工具-layoutopt。这是android为我们提供的布局分析工具。它能分析指定的布局，然后提出优化建议。　　

要运行它，打开命令行进入sdk的tools目录，输入layoutopt加上你的布局目录命令行。运行后如图所示，框出的部分即为该工具分析布局后提出的建议，这里为建议替换标签。

<img src="/media/img/android-view-5.jpg">

下面列出一些常会碰到的输出：

    $ layoutopt samples/
    samples/compound.xml
       7:23 The root-level <FrameLayout/> can be replaced with <merge/>
       11:21 This LinearLayout layout or its FrameLayout parent is useless
    samples/simple.xml 提示未使用到该布局
       7:7 The root-level <FrameLayout/> can be replaced with <merge/>
    samples/too_deep.xml
       -1:-1 This layout has too many nested layouts: 13 levels, it should have <= 10!
       20:81 This LinearLayout layout or its LinearLayout parent is useless
       24:79 This LinearLayout layout or its LinearLayout parent is useless
       28:77 This LinearLayout layout or its LinearLayout parent is useless
       32:75 This LinearLayout layout or its LinearLayout parent is useless
       36:73 This LinearLayout layout or its LinearLayout parent is useless
       40:71 This LinearLayout layout or its LinearLayout parent is useless
       44:69 This LinearLayout layout or its LinearLayout parent is useless
       48:67 This LinearLayout layout or its LinearLayout parent is useless
       52:65 This LinearLayout layout or its LinearLayout parent is useless
       56:63 This LinearLayout layout or its LinearLayout parent is useless
    samples/too_many.xml
       7:413 The root-level <FrameLayout/> can be replaced with <merge/>
       -1:-1 This layout has too many views: 81 views, it should have <= 80!
    samples/useless.xml 提示该布局中有太多的控件
       7:19 The root-level <FrameLayout/> can be replaced with <merge/>
       11:17 This LinearLayout layout or its FrameLayout parent is useless 
