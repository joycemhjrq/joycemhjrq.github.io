---
layout: post
title: Android junit report
categories: Android
tags: junit
---

<http://zutubi.com/source/projects/android-junit-report/>

## quick start

For the impatient, here is an overview of how to integrate the runner with Ant builds. Note all modifications are made to your test project, i.e. the project which implements the JUnit tests:

Grab the jar from the downloads page and add it to your libs/ directory. 
Edit AndroidManifest.xml to set android:name in the tag to: com.zutubi.android.junitreport.JUnitReportTestRunner. 
Edit ant.properties to add the line: 
test.runner=com.zutubi.android.junitreport.JUnitReportTestRunner

Run your tests as you would normally: 
$ ant debug install test

Pull the resulting XML report from the device (from the application under test's internal storage directory): 
$ adb pull /data/data//files/junit-report.xml

Integrate the XML with your chosen build tool. 
In practice you may want to tweak the report location, add the last pull step to your build, and more. For details, see the documentation page.

注意这里的：/data/data//files/junit-report.xml这个地址要查看模拟器上生成的junit-report.xml的地址，之前这个也弄了好久，发现这里用${project.app.package}代替得到的值跟实际的值有差如，实际模拟器上的应该是com.ylzinfo.ydhl.ejf，但是用${project.app.package}得到的值是com.ylzinfo.ydhl.ejf.test，所以刚开始用这个的时候一直报找不到junit-report.xml，搞了好久，如何得到这个xml的位置呢，最好到模拟器上自己去查看，是否真的有生成：

1、以DDMS格式查看项目：eclipse的Window-->Show Perspective-->DDMS

2、在右边的File Explorer目录下查看 data/data目录下相应的包和目录下哪个里面有junit-report.xml就用哪个


