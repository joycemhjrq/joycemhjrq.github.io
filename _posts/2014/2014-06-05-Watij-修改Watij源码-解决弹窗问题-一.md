---
layout: post
title: Watij 修改Watij源码 解决弹窗问题 一
categories: Watij
tags: watij
---

最近刚开始研究watij,主要原因是我想在我自己的项目中使用自动化ST,无意中看到了这个watij的介绍,感觉使用起来很方便,但是当我试图将它引入我的项目当中时却发现一些不爽的地方:

问题一: 点击一个按钮出现弹出框时,程序阻塞,无法继续进行.

当时非常郁闷,如果一出现弹出框就无法进行下去了,那还玩个鬼呀,于是打开下载的watij3.2.1的源码包,找到他自己提供的单元测试代码,看看它自己是怎么处理的(src\java-test包下为单元测试类),就在他watij包下找到了PopupsTest类,一看才明白,原来人家是直接new了一个线程出来触发click事件,无语...看来我也只能这么用了,于是我就添加了两个类来解决这个问题:

Java代码

{% highlight java %}

package watij.utilities;  
  
import watij.elements.HtmlElement;  
  
  
public class AlertDialogEvent {  
  
    public static void fire(final HtmlElement btn) {  
        new Thread(new ClickThread(btn)).start();  
        try {  
            // 停一小会看一下效果  
            Thread.sleep(100);  
        } catch (InterruptedException e) {  
            Debug.handleException(e);  
        }  
    }  
}  

package watij.utilities;

import watij.elements.HtmlElement;


public class AlertDialogEvent {

        public static void fire(final HtmlElement btn) {
                new Thread(new ClickThread(btn)).start();
                try {
            // 停一小会看一下效果
                        Thread.sleep(100);
                } catch (InterruptedException e) {
                        Debug.handleException(e);
                }
        }
}

{% endhighlight %}
 
Java代码

{% highlight java %}

package watij.utilities;  
  
import watij.elements.HtmlElement;  
  
  
public class ClickThread implements Runnable {  
  
    private HtmlElement btn;  
  
    public ClickThread(HtmlElement btn) {  
        this.btn = btn;  
    }  
  
    public void run() {  
        try {  
            this.btn.click();  
        } catch (Exception e) {  
            Debug.handleException(e);  
        }  
    }  
}  

package watij.utilities;

import watij.elements.HtmlElement;


public class ClickThread implements Runnable {

        private HtmlElement btn;

        public ClickThread(HtmlElement btn) {
                this.btn = btn;
        }

        public void run() {
                try {
                        this.btn.click();
                } catch (Exception e) {
                        Debug.handleException(e);
                }
        }
}

{% endhighlight %}

这样以后再碰到要触发弹出框的时候我就用如下写法:
Java代码

    AlertDialogEvent.fire(ie.button(SymbolFactory.name, value));  

    AlertDialogEvent.fire(ie.button(SymbolFactory.name, value));


相对来说还是比较方便的.

问题二: 中文支持问题,刚刚我们已经可以点击并触发弹出框了,但是我却触发不到弹出框中的确认按钮?郁闷,如,我触发的是一个AlertDialog,我执行
Java代码

    ie.alertDialog().ok();  

    ie.alertDialog().ok();

却 没有反应,于是我就gg了一下,发现问此问题的同仁还不少,在网上逛了一下,有人提出直接操作底层控件,将IE的alertDialog的"确定"修改 为"OK",牛人,但是我拿来试了一下,没有效果,不知道是不是我用错了,有人说直接修改源码,修改IEAlertDialog类,将
Java代码

    public void ok() throws Exception {  
        Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "OK");  
        IEUtil.clickWindowUntilGone(wnd, dialog);  
    }  

    public void ok() throws Exception {
        Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "OK");
        IEUtil.clickWindowUntilGone(wnd, dialog);
    }

修改为:
Java代码

    public void ok() throws Exception {  
        Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "确定");  
        IEUtil.clickWindowUntilGone(wnd, dialog);  
    }  

    public void ok() throws Exception {
        Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "确定");
        IEUtil.clickWindowUntilGone(wnd, dialog);
    }


我觉得挺好的,于是乎就照着修改,我还顺便打算把IEFileDownloadDialog,IEPromptDialog也修改了一下,可在修 改的时候却发现,除了IEAlertDialog类是直接写死了按钮的value值外,其他的两个弹出窗口的按钮的value是从资源文件中读取的,呵 呵,这下把我乐坏了, 原来人家支持国际化呀,于是我就解压jar包,在watij目录下果 然发现了两个资源文件:WatijResources.properties和WatijResources_de.properties,于是我就复制 了一份,另存为WatijResources_zh.properties,将里面对应的value修改为相应的中文,另外我还添加了一 条,IEAlertDialog_OK="确定",然后修改IEAlertDialog类,将按钮value的值从"OK"修改为从资源文件中获取,然后 在WatijResources类中添加一行代码:
Java代码

    IEAlertDialog_OK("IEAlertDialog_OK")  

    IEAlertDialog_OK("IEAlertDialog_OK")


相关文件修改如下:

WatijResources_zh.properties:

Java代码

    IEAlertDialog_OK=\u786E\u5B9A  
    IEConfirmDialog_Cancel=\u53D6\u6D88  
    IEFileDownloadDialog_Cancel=\u53D6\u6D88  
    IEFileDownloadDialog_Close=\u5173\u95ED  
    IEFileDownloadDialog_CloseWhenComplete=\u4E0B\u8F7D\u5B8C\u6BD5\u540E\u5173\u95ED\u8BE5\u5BF9\u8BDD\u6846(&C)  
    IEFileDownloadDialog_Open=\u6253\u5F00(&O)  
    IEFileDownloadDialog_Save=\u4FDD\u5B58(&S)  
    IEFileDownloadDialog_Title_DownloadComplete=\u4E0B\u8F7D\u5B8C\u6BD5  
    IEFileDownloadDialog_Title_FileDownload=\u6587\u4EF6\u4E0B\u8F7D  
    IEFileDownloadDialog_Title_SaveAs=\u53E6\u5B58\u4E3A  
    IEFileField_ChooseFile=\u9009\u62E9\u6587\u4EF6  
    IEPromptDialog_Title_ExplorerUserPrompt=Explorer \u7528\u6237\u63D0\u793A  

    IEAlertDialog_OK=\u786E\u5B9A
    IEConfirmDialog_Cancel=\u53D6\u6D88
    IEFileDownloadDialog_Cancel=\u53D6\u6D88
    IEFileDownloadDialog_Close=\u5173\u95ED
    IEFileDownloadDialog_CloseWhenComplete=\u4E0B\u8F7D\u5B8C\u6BD5\u540E\u5173\u95ED\u8BE5\u5BF9\u8BDD\u6846(&C)
    IEFileDownloadDialog_Open=\u6253\u5F00(&O)
    IEFileDownloadDialog_Save=\u4FDD\u5B58(&S)
    IEFileDownloadDialog_Title_DownloadComplete=\u4E0B\u8F7D\u5B8C\u6BD5
    IEFileDownloadDialog_Title_FileDownload=\u6587\u4EF6\u4E0B\u8F7D
    IEFileDownloadDialog_Title_SaveAs=\u53E6\u5B58\u4E3A
    IEFileField_ChooseFile=\u9009\u62E9\u6587\u4EF6
    IEPromptDialog_Title_ExplorerUserPrompt=Explorer \u7528\u6237\u63D0\u793A

watij.utilities.WatijResources类

Java代码

{% highlight java %}

package watij.utilities;  
 
public enum WatijResources {  
    IEAlertDialog_OK("IEAlertDialog_OK"),  
    IEConfirmDialog_Cancel("IEConfirmDialog_Cancel"),  
    IEFileDownloadDialog_Cancel("IEFileDownloadDialog_Cancel"),  
    IEFileDownloadDialog_Close("IEFileDownloadDialog_Close"),  
    IEFileDownloadDialog_Open("IEFileDownloadDialog_Open"),  
    IEFileDownloadDialog_CloseWhenComplete("IEFileDownloadDialog_CloseWhenComplete"),  
    IEFileDownloadDialog_Save("IEFileDownloadDialog_Save"),  
    IEFileDownloadDialog_Title_DownloadComplete("IEFileDownloadDialog_Title_DownloadComplete"),  
    IEFileDownloadDialog_Title_FileDownload("IEFileDownloadDialog_Title_FileDownload"),  
    IEFileDownloadDialog_Title_SaveAs("IEFileDownloadDialog_Title_SaveAs"),  
    IEFileField_ChooseFile("IEFileField_ChooseFile"),  
    IEPromptDialog_Title_ExplorerUserPrompt("IEPromptDialog_Title_ExplorerUserPrompt");  
  
    private String key;  
  
    WatijResources(String key) {  
        this.key = key;  
    }  
  
    public String getKey() {  
        return key;  
    }  
}  

package watij.utilities;


public enum WatijResources {
    IEAlertDialog_OK("IEAlertDialog_OK"),
    IEConfirmDialog_Cancel("IEConfirmDialog_Cancel"),
    IEFileDownloadDialog_Cancel("IEFileDownloadDialog_Cancel"),
    IEFileDownloadDialog_Close("IEFileDownloadDialog_Close"),
    IEFileDownloadDialog_Open("IEFileDownloadDialog_Open"),
    IEFileDownloadDialog_CloseWhenComplete("IEFileDownloadDialog_CloseWhenComplete"),
    IEFileDownloadDialog_Save("IEFileDownloadDialog_Save"),
    IEFileDownloadDialog_Title_DownloadComplete("IEFileDownloadDialog_Title_DownloadComplete"),
    IEFileDownloadDialog_Title_FileDownload("IEFileDownloadDialog_Title_FileDownload"),
    IEFileDownloadDialog_Title_SaveAs("IEFileDownloadDialog_Title_SaveAs"),
    IEFileField_ChooseFile("IEFileField_ChooseFile"),
    IEPromptDialog_Title_ExplorerUserPrompt("IEPromptDialog_Title_ExplorerUserPrompt");

    private String key;

    WatijResources(String key) {
        this.key = key;
    }

    public String getKey() {
        return key;
    }
}

{% endhighlight %}

watij.runtime.ie.IEAlertDialog修改为:

Java代码

{% highlight java %}

package watij.runtime.ie;  
  
import watij.dialogs.AlertDialog;  
import watij.utilities.WatijResourceLoader;  
import watij.utilities.WatijResources;  
  
import com.jniwrapper.win32.ui.Wnd;  
  
public class IEAlertDialog extends IEBaseDialog implements AlertDialog {  
  
    public static AlertDialog findAlertDialog(IE ie) throws Exception {  
        Wnd wnd = IEUtil.waitDialogWnd(IE.TITLE);  
        return new IEAlertDialog(wnd, ie);  
    }  
  
    public IEAlertDialog(Wnd dialog, IE ie) {  
        super(dialog, ie);  
    }  
      
    public void ok() throws Exception {  
        Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", WatijResourceLoader.getString(WatijResources.IEAlertDialog_OK));  
        IEUtil.clickWindowUntilGone(wnd, dialog);  
    }  
}  

package watij.runtime.ie;

import watij.dialogs.AlertDialog;
import watij.utilities.WatijResourceLoader;
import watij.utilities.WatijResources;

import com.jniwrapper.win32.ui.Wnd;

public class IEAlertDialog extends IEBaseDialog implements AlertDialog {

    public static AlertDialog findAlertDialog(IE ie) throws Exception {
            Wnd wnd = IEUtil.waitDialogWnd(IE.TITLE);
            return new IEAlertDialog(wnd, ie);
    }

    public IEAlertDialog(Wnd dialog, IE ie) {
            super(dialog, ie);
    }
    
    public void ok() throws Exception {
            Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", WatijResourceLoader.getString(WatijResources.IEAlertDialog_OK));
            IEUtil.clickWindowUntilGone(wnd, dialog);
    }
}

{% endhighlight %}

ok,最后将资源文件放到watij包下面,编写一个ant脚本将该源码打成jar包,然后引用到工程当中去,不过在使用的时候我们要设置一个环境变量:
Java代码

    System.getProperty("browser.ui.locale", "zh")  

    System.getProperty("browser.ui.locale", "zh")


这样watij就会读到中文的资源文件了,弹出框中文按钮点不着的问题也就解决了,

呼,终于可以用的比较顺手了,再针对自己的项目封装一层,方便编写适合自己项目的ST代码,差不多一个操作一行代码,OK,收工!

如果各位不想修改源代码，可以安装上面的包路径创建相应的文件和java类来覆盖，也能达到效果。