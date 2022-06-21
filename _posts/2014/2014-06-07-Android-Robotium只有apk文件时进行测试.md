---
layout: post
title: Android Robotium只有apk文件时进行测试
categories: Android
tags: 
    - robotium
    - apk
---

## 首先：设置环境变量

1. 配置ANDROID_HOME为android sdk的安卓目录，例如：D:\android-sdk

2. 在path下添加这两个：

`%ANDROID_HOME%\tools;%ANDROID_HOME%\platform-tools`;

## 其次： 安装签名文件

1. 因为robotium要求被测应用和测试代码要有一致的签名, 所以我们需要把下载到的apk，通过re-sign.jar来产生debug key的apk，这个重新生成的apk就会跟测试项目签名一致了

re-sign.jar可以从这里下载到：

http://www.troido.de/re-sign.jar

2. 下载完后，在命令行下 通过 java -jar re-sign.jar就会出现一个节目，然后将apk拖到这个节目，就会自动生成一个debug key的apk

产生新apk的过程中会弹出一个信息框，记得截下图，因为里面有两个信息我们等会的代码中需要用到

3. 安装产生的apk。然后打开模拟器（模拟器器一定要打开才能安装成功），然后打开命令行  adb install mitalk_debug.apk(新生成apk的名称) ， 或者双击apk文件也可以安装

安装成功就可以再模拟器里看到该应用的图标了

## 然后：创建项目(使用米聊apk)

1.打开Eclipse，点击File->New一个Android Test Project  ApkTest, 然后点击下一步的时候选择This project(因为我们没有米聊应用的源码)，然后选择要在哪个android版本上测试

2.在该项目下创建一个包，com.mitalk.test,在该包下创建LoginTest类，如下

{% highlight java %}

  package com.mitalk.test;

  //需要导入的包

  import android.app.Activity;

  import android.test.ActivityInstrumentationTestCase2;

  import com.jayway.android.robotium.solo.Solo;

  @SuppressWarnings("rawtypes")

  public class LoginTest extends ActivityInstrumentationTestCase2 {

  //定义变量

  public Solo solo;

  public Activity activity;

  private static Class<?> launchActivityClass;

  //对应re-sign.jar生成出来的信息框里的两个值

  private static String mainActiviy = "com.xiaomi.channel.ui.ChannelLauncherActivity";

  private static String packageName = "com.xiaomi.channel";

  static {

  try {

  launchActivityClass = Class

  .forName(mainActiviy);

  } catch (ClassNotFoundException e) {

  throw new RuntimeException(e);

  }

  }

  @SuppressWarnings("unchecked")

  public LoginTest() {

  super(packageName, launchActivityClass);

  }

  @Override

  protected void setUp() throws Exception {

  super.setUp();

  this.activity = this.getActivity();

  this.solo = new Solo(getInstrumentation(), getActivity());

  }

  public void testLoginWithIncorrentUsernameAndPassword() {

    //一定时间内等待text出现，否则就出错
    solo.waitForText("Log In", 1, 10000);

    //点击text “Log In”
    solo.clickOnText("Log In");

    //一定时间内等待text出现，否则就出错
    solo.waitForText("OK", 1, 2000);

    //输入text
    solo.enterText(0, "13559494170");
    solo.enterText(1, "123456");

    //点击text “OK”
    solo.clickOnText("OK");

    //等待出现下面的text后，进行验证
    solo.waitForText("Incorrect ID number or password.", 1, 10000);
    assertTrue(solo.searchText("Incorrect ID number or password.",true));

  }

  @Override

  public void tearDown() throws Exception {

  try {

  this.solo.finishOpenedActivities();

  } catch (Throwable e) {

  e.printStackTrace();

  }

  this.activity.finish();

  super.tearDown();

  }

  }

{% endhighlight %}

3.右键该项目，选择property然后选择java build path, 选择 Add External JARs,选择下到的robotium.jar

4.在跑测试用例之前，还需要修改下AndroidManifest.xml文件的android:targetPackage为被测应用的根的包名

    <instrumentation
      android:name="android.test.InstrumentationTestRunner"
      android:targetPackage="com.xiaomi.channel" />

## 最后： run as android junit test，就可以进行测试了

## Robotium 只有apk测试 通过id获取view

    Activity a  = solo.getCurrentActivity();
    int id = a.getResources().getIdentifier("bottom_menu_show_menu_button", "id", a.getPackageName());
    final View v = a.findViewById(id);
    solo.clickOnView(v);//即点击id为bottom_menu_show_menu_button的view
