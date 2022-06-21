---
layout: post
title: Selenium 在启动firefox时加载扩展
categories: Selenium
tags: firefox
---

当我们需要在用firefox进行测试时启动firebug,可以使用下面的代码


    File file = new File("xpi\\firebug-1.8.1.xpi");
    FirefoxProfile firefoxProfile = new FirefoxProfile();
    firefoxProfile.addExtension(file);
    firefoxProfile.setPreference("extensions.firebug.currentVersion", "1.8.1"); 

    WebDriver driver = new FirefoxDriver(firefoxProfile);


以此类推，当我们需要启用别的扩展程序时，换下文件就可以了

处理firefox下载弹出框的问题，通过以下代码，可以直接将文件下载到指定目录而不会出现下载框

    FirefoxProfile profile = new FirefoxProfile();
    profile.setPreference("browser.download.dir", DOWNLOAD_FILE_PATH);//指定下载路径
    profile.setPreference("browser.download.folderList", 2);
    profile.setPreference("browser.download.manager.showWhenStarting",false);
    profile.setPreference("browser.helperApps.neverAsk.saveToDisk","text/csv");//指定下载文件格式
    WebDriver driver = new FirefoxDriver(profile); 