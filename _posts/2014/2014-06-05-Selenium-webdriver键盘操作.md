---
layout: post
title: Selenium webdriver键盘操作
categories: Selenium
tags: Action
---

在测试工具webdriver的使用过程中，应该会用到使用工具来模拟用的鼠标、键盘的一些输入操作，比如说：

1、鼠标的左键点击、双击、拖拽、右键点击等；

2、键盘的回车、回退、空格、ctrl、alt、shift等；

在webdriver中，有专门的一个类，是用来进行鼠标、键盘的模拟操作的，那就是Actions类，该类使用时，又会涉及到Keyboard、Mouse、CompositeAction（复合动作），先对Mouse的方法做简单罗列，然后再用代码说明：

1、鼠标左键点击：

    Actions action = new Actions(driver);
    action.click(driver.findElement(By.xpath(xpath)));

左键点击是普通常用的方法，前面已经介绍过一种更简单的方法：

    Driver driver=new Driver();
    driver.findElement(By.xpath(xpath)).click();

2、鼠标左键双击：

    Actions action = new Actions(driver);
    action.doubleClick(driver.findElement(By.xpath(xpath)));

3、鼠标左键按下操作：

    Actions action = new Actions(driver);
    action.clickAndHold(driver.findElemen(By.xpath(xpath)));

4、鼠标左键移动到元素操作：

    Actions action = new Actions(driver);
    action.moveToElement(driver.findElement(By.xpath(xpath)));

5、鼠标右键点击操作：

    Actions action = new Actions(driver) ;
    action.contextClick(driver.findElement(By.xpath(xpath)));

6、组合的鼠标操作（将目标元素拖拽到指定的元素上）：

    Actions action = new Actions(driver);
    action.dragAndDrop(driver.findElement(By.xpath(xpath)),driver.findElement(By.xpath(xpath)));

7、组合的鼠标操作（将目标元素拖拽到指定的元素上）：

    Actions action = new Actions(driver);
    action.dragAndDrop(driver.findElement(By.xpath(xpath)),xOffset,yOffset);

代码演示：

    driver.get("http://www.baidu.com");
    driver.findElement(By.name("wd")).sendKeys("林丹");

//点击操作

    driver.findElement(by.id("su")).click();
    driver.navigate().back();
    Actions action=new Actions(driver);
    action.click(driver.findElement(by.id("su")));

//双击操作（略）

//拖拽选择操作，鼠标按住不放，进行拖拽选择，然后释放鼠标，需要三个动作

    action.clickAndHold(driver.findElement(By.id("1"))).moveToElement(driver.findElement(By.id("3"))).perform();
    action.release();

//拖拽元素，将一个元素拖拽到另一个元素上，然后释放鼠标（略）

键盘的模拟操作，包括普通按键，比如enter、backspace、tab等，还包括四个修饰键（Modifier Keys），分别是Caps Lock，Control，Option，Command。
普通按键使用时，直接使用sendkeys(theKeys)就可以，如按下enter键：

    action.sendKeys(Keys.ENTER).perform();

修饰键一般使用时，是和别的按键配合使用的，比如快捷键ctrl+F4，这时就得使用keyDown(theKeys)、keyUp(theKeys)来操作，代码如下：

    driver.get("http://www.baidu.com");
    driver.findElement(by.name("wd")).sendKeys("林丹");
    Actions action=new Actions(driver);
    //输入框中输入内容，然后点击回车进行百度
    action.sendKeys(Keys.ENTER).perform();
    //使用快捷键alt+f4关闭窗口（但是该方法不稳定，时行时不行，不行居多）
    action.keyDown(keys.ATL).keyDown(keys.F4).keyUp(keys.ALT).perform();
    //使用ctrl+a全选
    driver.findElement(By.id("kw")).click();
    action.sendKeys(Keys.CONTROL+"a").perform();

## Tips：

    1、拖拽选择时，使用clickAndHold和moveToElement时，会受到鼠标所在位置的影响，所以尽量是两个方法一起使用；
    2、拖拽元素时，dragAndDrop方法，我没有找到实现的场景，所以没有写代码试验；
    3、直接按下普通按键时，直接使用sendkeys(theKeys)方法就能够实现；
    4、需要使用修饰键时，需要连贯动作中应该使用keydown和keyup方法；
    action.keyDown(keys.ATL).sendKeys(keys.F4).keyUp(keys.ALT).perform();//这是不能实现alt+f4关闭窗口的效果的，只能实现单独按f4的效果
    5、对于一些快捷键，如ctrl+a，可以直接sendkeys(Keys.CONTROL+"a")。