---
layout: post
title: Android Appium 常见问题
categories: Android
tags: appium
---

今天打开appiumForWindow后，用python打开android设备上的客户端，出现问题如下：

## 问题一：

selenium.common.exceptions.WebDriverException: Message: u'A new session could not be created. (Original error: Could not extract PIDs from ps output. PIDS: [], Procs: ["/system/bin/sh: grep: not found"])'

解决方法：

    Edit the file adb.js (in {Appium application package}/Contents/Resources/node_module/appium/lib/devices/android/adb.js)
    Search for the function ADB.prototype.getPIDsByName
    Replace the line:
    this.shell("ps | grep '" + name + "'", function(err, stdout) {
    with
    this.shell("ps '" + name + "'", function(err, stdout) {
    Add procs.shift(); after the line var procs = stdout.split("\n");
    var procs = stdout.split("\n");
    procs.shift();
    Save the file, and restart Appium server.
    It should looks like this in final:
    this.shell("ps '" + name + "'", function(err, stdout) {
    if (err) return cb(err);
    stdout = stdout.trim();
    if (!stdout) {
    logger.info("No matching processes found");
    return cb(null, []);
    }
    var procs = stdout.split("\n");
    procs.shift();

## 问题二：查看android 设备或者emulator页面的元素

原文如下：

    [ Android SDK Tools, Revision 21 or higher ]
    [ Android SDK Platform, API 16 or higher ]

1.连接上设备或者模拟器，然后切换到android tools目录下，启动uiautomatorviewer.bat

2. Ui Automator Viewer打开后点击按钮 Device Screenshot就可以看到当前视图，在右侧可以看到视图的元素

原文请参考:<http://stackoverflow.com/questions/15647687/enumerate-all-elements-in-selenium-python-bindings-for-appium>