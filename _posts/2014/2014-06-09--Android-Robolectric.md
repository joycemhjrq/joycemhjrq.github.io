---
layout: post
title: Android Robolectric
categories: Android
tags: robolectric
---

官网地址：<http://robolectric.org/>（有时候速度太慢，可以试试用代理）

## Using Eclipse:

Eclipse will try to use the Android JUnit test runner by default for Android projects, so you will need a separate test project to run the test with Robolectric, and set up a run configuration to run the tests with the Eclipse JUnit Launcher.

    •Create a new Java project in parallel with your app's project, as your test project. For example, if your app is named MyApp, you might create a MyAppRobolectricTest project.

    •In many cases it will be advantageous to keep the source code for the tests under the same root folder as the source for the rest of the project. To make this work, use the "Link Source..." button in the Build Path dialog to create a link from this test project to the source root for the tests under the main project.

    •Add the appropriate Android SDK jars to the test project's build path (e.g. {android sdk root}/platforms/android-8/android.jar and {android sdk root}/add-ons/addongoogleapisgoogleinc_8/libs/maps.jar)

    • Download robolectric-X.X.X-jar-with-dependencies.jar place it in your test project and add it to the build path, along with the JUnit library.

    •Add your app's project as a project dependency to the build path of your test project.

    •Important! Add a new JUnit run/debug launch configuration to run the tests in the test folder. There may be a warning that multiple launchers are available, make sure to select the Eclipse JUnit Launcher instead of the Android JUnit Launcher. Set the working directory to be the root of your main project, not that of your test project.

简单来说：Robolectric是Android项目的单元测试工具，它区别于Andoird项目，因为它不用起模拟器或者真机进行测试

首先建立一个Java Project，导入需要的包（android sdk 的android.jar和maps.jar见上面，还有一个robolectric的包：robolectric-X.X.X-jar-with-dependencies.jar），那现在的问题就是这个java project 如何与Android相关联：

1、Android项目下建立一个test的Source folder，然后在java project 的build path下，source link关联到这个test的包(可以不用建立source link,这步可以不要)，然后(build path下)Project里面也要建立Android项目依赖，然后：

<http://blog.csdn.net/wangjia55/article/details/8793084>

## Create a test Run Configuration

Your tests will not run without this step. Your resources will not be found.

    •“Run” → “Run Configurations…”
    •Double click ”JUnit” (not “Android JUnit Test”)
    •Name: MyProjectTestConfiguration
    •Select the “Run all tests in the selected project, package or source folder:” radio button
    •Click the “Search” button
    •Select “MyProjectTest”
    •TestRunner: JUnit 4
    •Click on the link “Multiple launchers available Select one…” at the bottom of the dialog
    •Check the “Use configuration specific settings” box
    •Select “Eclipse JUnit Launcher”
    •Click “OK”
    •Click the “Arguments” tab
    •Under “Working directory:” select the “Other:” radio button
    •Click “Workspace…”
    •Select “MyProject” (not “MyProjectTest”, The value inside of ‘Other’ edit box should be ‘${workspace_loc:MyProject}’)
    •Click “OK”
    •Click “Close”

## Verify your setup

    •Right click the “test” folder under “MyProjectTest”
    •Select “New”→“Class”
    •Package: “com.example”
    •Name: “MyActivityTest”
    •Click “Finish”
    •Add the following source:

{% highlight java %}

package com.example;
import com.example.MyActivity;
import com.example.R;
import com.xtremelabs.robolectric.RobolectricTestRunner;
import org.junit.Test;
import org.junit.runner.RunWith;
import static org.hamcrest.CoreMatchers.equalTo;
import static org.junit.Assert.assertThat;

@RunWith(RobolectricTestRunner.class)

public class MyActivityTest {

 @Test
    public void shouldHaveHappySmiles() throws Exception {

    String hello = new MyActivity().getResources().getString(R.string.hello);

assertThat(hello, equalTo("Hello World, MyActivity!"));
    }
}

{% endhighlight %}

To run the tests

    •“Run” → “Run Configurations…”
    •Select “JUnit” → “MyProjectTestConfiguration”
    •Click “Run“


问题汇总：

在整个过程中出现了以下的问题：

### 一.在运行单元测试用列MyActivity的时候提示  "../MyProject/./AndroidManifest.xml”

解决措施：

    •Right click on the test
    •“Run As” → “Run Configurations…”
    •Double click “JUnit” (this will magically make the test you’re running appear under JUnit)
    •Select “MyActivityTest” (or the name of whichever test you are currently trying to run)
    •TestRunner: JUnit 4
    •Click on the link “Multiple launchers available Select one…” (or it also may appear as “Using XXX Launcher - Select other…”) at the bottom of the dialog
    •Check the “Use configuration specific settings” box
    •Select “Eclipse JUnit Launcher”
    •Click “OK”
    •Click the “Arguments” tab
    •Under “Working directory:” select the “Other:” radio button
    •Click “Workspace…”
    •Select “MyProject” (not “MyProjectTest”)
    •Click “OK”
    •Click “Close”

### 二 提示 “no such layout/main” 

<b>if you get a RuntimeException saying: “no such layout layout/main”</b>

It means that you have tried to run a test for which you do not have a Run Configuration set up. To remedy this:

    •Right click on the test
    •“Run As” → “Run Configurations…”
    •Double click “JUnit” (this will magically make the test you’re running appear under JUnit)
    •Select “MyActivityTest” (or the name of whichever test you are currently trying to run)
    •TestRunner: JUnit 4
    •Click on the link “Multiple launchers available Select one…” (or it also may appear as “Using XXX Launcher - Select other…”) at the bottom of the dialog
    •Check the “Use configuration specific settings” box
    •Select “Eclipse JUnit Launcher”
    •Click “OK”
    •Click the “Arguments” tab
    •Under “Working directory:” select the “Other:” radio button
    •Click “Workspace…”
    •Select “MyProject” (not “MyProjectTest”)
    •Click “OK”
    •Click “Close”

### 三 按照上述的方法解决了“AndroidManifest.xml”找不到的问题，但是又提示“Android sdk 的路径找不到” 

解决措施：

在你工程的根目录下，加上local.properties , 加上 sdk.dir=/home/wangjia/android-sdk ，即可

Robolectric cannot find your Android SDK. You can tell Robolectric how to find your SDK root in several ways:

local.properties file

Set the sdk.dir in a local.properties file by running the following in your project’s root dir:

    $ android update project -p .


Setting up a local.properties file is a solution that will work for most IDEs since you don’t need to worry about getting environment variables passed around.

### 四。其他问题汇总（官网上有汇总）

<b>Troubleshooting</b>

<b>java.lang.RuntimeException: Stub!</b>

Make sure that robolectric and its dependencies (including JUnit) appear before the Android API jars in the classpath.

<b>Unable to find Android SDK</b>

Robolectric cannot find your Android SDK. You can tell Robolectric how to find your SDK root in several ways:

 local.properties file

Set the sdk.dir in a local.properties file by running the following in your project’s root dir:

    $ android update project -p .


Setting up a local.properties file is a solution that will work for most IDEs since you don’t need to worry about getting environment variables passed around.

 <b>ANDROID_HOME environment variable</b>

Set ANDROID_HOME environment variable. You can put this in your .bash_profile for example. You may need to do some extra work to get your IDE to pick it up.

export ANDROID_HOME=/path/to/android/sdk


 android.sdk.path system property

Set the Java system property android.sdk.path, e.g. by putting -Dandroid.sdk.path=/path/to/android/sdk on the command line.

 which android

As a last resort, Robolectric will try running which android to find the executable on your path. Add the SDK tools to your path:

PATH=/path/to/android/sdk/tools:$PATH


<b>Type com.google.android.maps.MapView not present</b>

    java.lang.TypeNotPresentException:
    Typecom.google.android.maps.MapView not present at
    org.robolectric.Robolectric.bindShadowClass(Robolectric.java:67)Caused
    by: java.lang.ClassNotFoundException: caught an exception while
    obtaining a class file for com.google.android.maps.MapView...

1.Make sure you have the Google Maps API jar in your build path.

2.Even if you’re building against an earlier version of the API, link Robolectric to version 7 or higher.

<b>Could not resolve dependencies for project: Could not find artifact com.google.android.maps:maps:jar:16_r2 in central (http://repo1.maven.org/maven2)</b>

The jerk lawyers at Google won’t allow the Google maps add-on library stubs to be uploaded to Maven Central. You need to manually install them yourself.

Make sure you’ve got Android SDK 16 or later downloaded, then do this:

    cd $ANDROID_HOME
    ls -1d add-ons/addon-google_apis-google-* | sort | tail -1 |
        xargs -I% mvn install:install-file -DgroupId=com.google.android.maps -DartifactId=maps -Dversion=16_r2 -Dpackaging=jar -Dfile=%/libs/maps.jar

