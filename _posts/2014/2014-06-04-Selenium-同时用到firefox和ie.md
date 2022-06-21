---
layout: post
title: Selenium 同时用到firefox和ie
categories: Selenium
tags: 
    - ie
    - firefox
---

## selenium 同一个类是需要用到firefox和ie怎么办

	driver.get("http://developer.ylz.com:14072/developer/getAllApplicationByUser.html");
	inputById("username", "c");
	inputById("password", "a");
	Selenium selenium=new WebDriverBackedSelenium(driver, "");
	selenium.click("id=submit");
	clickAndSwitch(By.id("submit"));
	clickAndSwitch(By.linkText("修改"));
	clickAndSwitch(By.id("submitButton"));
	
//需要用IE操作的地方调用IE：

	driverIE=new InternetExplorerDriver();
	driverIE.get("http://developer.ylz.com:14072/developer/getAllApplicationByUser.html");
	driverIE.findElement(By.id("username")).sendKeys("c");
	driverIE.findElement(By.id("password")).sendKeys("a");
	driverIE.findElement(By.id("submit")).click();
	driverIE.get("http://developer.ylz.com:14072/developer/updateBasic.html?application.appId=3004&applicationTmp.appTmpId=1002&application.appName=电");
	driverIE.findElement(By.name("inputImgFiles")).sendKeys("G:\\工作\\民生网msw\\3rd-创建,申请应用\\图片上传测试\\pictureOfAppScreenshot\\1.png");
	pause(5000L);
	driverIE.findElement(By.xpath("//img[@alt='立即上传']")).click();
	driverIE.findElement(By.xpath("//img[@alt='立即上传']")).click();
	driverIE.close();
	//然后接着用Firefox操作
	inputById("","");
	......


