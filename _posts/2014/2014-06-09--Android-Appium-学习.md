---
layout: post
title: Android Appium 学习
categories: Android
tags: appium
---

今天使用appium 来测试android客户端，使用的是selenium + webdriver+ java,发现客户端的引导页无法操作，搜索到内容如下:

## 1.滚动条的滑动（http://www.cnblogs.com/nova/articles/1091993.html）

    top=document.documentElement.scrollTop;
    left=document.documentElement.scrollLeft;  

例如：

    String setscroll = "document.documentElement.scrollTop=" + height;             
    JavascriptExecutor jse=(JavascriptExecutor) driver;  
    jse.executeScript(setscroll); 

## 2.向下滑动一页

    Actions actions = new Actions(driver);
    actions.sendKeys(Keys.BACK_SPACE).perform();

## 3.鼠标滑动

    element = driver.findElement(By.cssSelector(".user-info.right>div>p>a"));
    Actions builder = new Actions(driver);
    builder.moveToElement(element).build().perform();
    //Thread.sleep(100); no need
    element = driver.findElement(By.cssSelector(".frd-del"));
    element.click();

## 4：加载ua

    private static WebDriver driver;
    FirefoxProfile profile = new FirefoxProfile();
    profile.setPreference("general.useragent.override",
          "Mozilla/5.0 (iPhone; U; CPU iPhone OS 3_0 like Mac OS X; en-us) AppleWebKit/528.18 (KHTML, like Gecko) Version/4.0 Mobile/7A341 Safari/528.16");
    driver = new FirefoxDriver(profile);

## 5：页面切换

    String pWindUrl = driver.getCurrentUrl();
    //跳至新打开的窗口
    if (driver.getWindowHandles().size() != 2) {fail(); }
    for (String handle : driver.getWindowHandles()) {
        driver.switchTo().window(handle);
        if (driver.getCurrentUrl().equals(pWindUrl)){
          driver.close(); //关闭父窗口
        } else {
                //6张图片
                if (driver.findElements(By.cssSelector(".img")).size() != 6) { fail(); }
        }
    }

## 6：alert提示

    private Alert alert;
    alert = driver.switchTo().alert();
    alert.accept();
    //alert.dismiss();

## 7：截屏
    try {
          File scrFile = ((TakesScreenshot) driver)
                      .getScreenshotAs(OutputType.FILE);
          FileUtils.copyFile(scrFile, new File("img\\Screenshot.png"));
    } catch (Exception e) {
          e.printStackTrace();
    } finally {
          // driver.close();
    }

## 8.查找当前页面的编辑区域

    textfields = driver.find_elements_by_tag_name("textfield")
    els=driver.find_elements_by_tag_name("text")

## 9.查找元素
    table =driver.find_element_by_tag_name("tableView")
    row = table.find_elements_by_tag_name("tableCell")[index]
    rows = table.find_elements_by_tag_name("tableCell")
    nav_bar = driver.find_element_by_tag_name("navigationBar")
    scroll = driver.find_element_by_tag_name("scrollview")
    webview = scroll.find_element_by_tag_name("webview")
    #find the URL field
    textfield = driver.find_element_by_name("URL entry")
    # get the window handles (webview)
    handle = self.driver.window_handles[0]
    self.driver.switch_to_window(handle)
    # Find the google logo
    logo = self.driver.find_element_by_id("hplogo")
    driver.execute_script("mobile: leaveWebView")
    # make screenshot and save it to the local filesystem
    success = self.driver.get_screenshot_as_file("foo.png")

## 10:执行脚本_Java
    Map<String, Double> tap = new HashMap<String, Double>();  
    tap.put("tapCount", new Double(2));
    tap.put("touchCount", new Double(1));  
    tap.put("duration", new Double(0.5));  
    tap.put("x", new Double(300));  
    tap.put("y", new Double(300));  
    driver.executeScript("mobile: tap", tap); 
    Python:
    driver.execute_script("mobile: tap", {"touchCount":"1", "x":"0.9", "y":"0.8", "element":element.id})
    滑动引导图从第一张到第二张(http://testerhome.com/topics/401)
    try:
        swipe_args = { 'startX':0.9, 'startY':0.5, 'endX':0.1, 'endY':0.5, 'duration':10}
        self.driver.execute_script("mobile: swipe", swipe_args)
    except Exception, e:
        print e
请参考:<https://github.com/appium/appium/blob/master/docs/gestures.md>

## 11.根据路径获取元素
    self.driver.find_elements_by_xpath("//ImageView")
    self.driver.find_elements_by_xpath("//TextView")

## 12.点击屏幕的某个点

self.driver.execute_script("mobile: tap", {"touchCount":"1", "x":22, "y":45}) #点击屏幕坐标为(22,45)的点1次