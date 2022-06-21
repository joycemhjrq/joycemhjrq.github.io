---
layout: post
title: Watij 修改Watij源码 解决弹窗问题 二
categories: Watij
tags: watij
---

这段时间在用Watij，版本是3.2.1，然而我发现它在处理弹窗方面并不如人意，于是对Watij的源代码做了些修改，终于可以处理大部分弹窗了。

首先，修改IEAlertDialog.java

将方法ok()改为：

    public void ok() throws Exception {
    //       Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "OK");
    //     Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "确定");
    String[] windowText = {"确定","是(Y)","OK","Yes"};
    Wnd wnd = IEUtil.waitDialogWndEx(dialog, "Button", windowText);
    IEUtil.clickWindowUntilGone(wnd, dialog);
    }

其次，修改IEConfirmDialog.java

将方法cancel()改为：

    public void cancel() throws Exception {
    //       Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button",
    //                 WatijResourceLoader.getString(WatijResources.IEConfirmDialog_Cancel));
    //     Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "取消");
    String[] windowText = {"取消","否(N)","Cancel","No"};
    Wnd wnd = IEUtil.waitDialogWndEx(dialog, "Button", windowText);
    IEUtil.clickWindowUntilGone(wnd, dialog);
    }

或者可以增加自定义方法：

    public void yes() throws Exception{
    Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "Yes");
    IEUtil.clickWindowUntilGone(wnd, dialog);
    }

    public void no() throws Exception{
    Wnd wnd = IEUtil.waitDialogWnd(dialog, "Button", "No");
    IEUtil.clickWindowUntilGone(wnd, dialog);
    }

然后，修改IEUtil.java

保持原有方法waitDialogWnd()不变，新增方法waitDialogWndEx()

{% highlight java %}

protected static Wnd waitDialogWndEx(final Wnd startWnd, final String windowClassName, final String[] windowText) throws Exception {
     class WindowReady implements Ready {
         Wnd wnd;
         
         public boolean isReady() throws Exception {
         int i = 0;
         while (i < windowText.length){
         wnd = Wnd.findWindowEx(startWnd, windowClassName, windowText[i++]);
         if (wnd.isWindow())
         break;
         }
             debugWnd(wnd, "findByStartWind");
             return wnd.isWindow();
         }

         public String getNotReadyReason() {
         StringBuffer reason = new StringBuffer();
         reason.append("Could not find Dialog with windowClassName=" + windowClassName + " windowText=");
         reason.append(windowText[0]);
         for (int i = 1; i < windowText.length; i++){
               reason.append("," + windowText[i]);
         }
         return reason.toString();
         }

         public Wnd wnd() {
             return wnd;
         }
     }
     WindowReady windowReady = new WindowReady();
     new WaiterImpl(10000, 200).waitUntil(windowReady);
     Wnd wnd = windowReady.wnd();
     return wnd;
 }

{% endhighlight %}

做完这些工作后，重新编译打包，输出一个新的watij.jar替换原有watij.jar

最后，新建一个自定义工程，在引用库那里导入watij.jar，创建主类HandleDialog.java

{% highlight java %}

import watij.runtime.ie.IE;
import watij.runtime.ie.IEConfirmDialog;
import watij.time.TimeException;
import watij.time.Timer;
import watij.time.TimerImpl;
import com.jniwrapper.win32.ui.Wnd;

public class HandleDialog {
static final int TIMEOUT_POPUP = 20000;
static final int WAIT_INCREMENT = 200;
static final String DIALOG_CLASSNAME = "#32770";
static final String IE_CLASSNAME = "IEFrame";
static final String[] windowTitle = {"Microsoft Internet Explorer","Windows Internet Explorer",
"Internet Explorer","Explorer 用户提示","添加到收藏夹","文件下载","File Download","安全警报","Security Alert"};
private IE ie;

public static void main(String[] args) {
this.ie = new IE();
this.ie.visible(false);

// 新建线程，处理弹出的对话框
handlePopupDialog(this.ie);
// 加载网页
this.ie.start("http://www.example.com");

}
  
  // handle the popup Dialog
  private void handlePopupDialog(final IE ie){
     // 新建线程守护IE，观察IE发生的事件
     new Thread( new Runnable(){
public void run(){
try{
// 该线程等待IE出现
ie.waitUntilExists();
if (ie.exists()){
class DialogReady implements MyReady {
Wnd wnd = null;
boolean ret = false;
public boolean isReady() throws Exception {
return ret; // 如果ret为true，结束等待；否则继续等待
     }
     public String getNotReadyReason() {
       return "there is no popup dialog";
     }
     public boolean actionWhenWait() throws Exception {
       ret = false;
       // 每次等待时都要判断IE是否还存在，因为IE有可能中途被关掉
if(ie.exists()){
int i = 0;
while (i < windowTitle.length){
wnd = Wnd.findWindow(DIALOG_CLASSNAME,windowTitle[i++]);
// 如果IE弹出对话框
if (wnd.isWindow()){
IEConfirmDialog confirm = new IEConfirmDialog(wnd,ie);
String dialogTitle = confirm.title();
// 如果弹窗是“文件下载”对话框，则关掉对话框；如果关掉对话框后IE为空页面，就关掉IE
if (dialogTitle.contains("文件下载") || dialogTitle.contains("File Download")){
//      confirm.cancel();
     confirm.quit();
     System.out.println(Thread.currentThread().getName()
     + "--->关掉“文件下载”对话框。");
     if (ie.getWebBrowser().getLocationURL().equalsIgnoreCase(Blank_URL)){
     ie.close();
     System.out.println(Thread.currentThread().getName()
     + "--->页面为空，关掉IE。");
     }
     } else if (dialogTitle.contains("安全警报") || dialogTitle.contains("Security Alert")){
//      confirm.cancel();
     confirm.ok();
     System.out.println(Thread.currentThread().getName()
     + "--->发生安全警报，点“确定”继续。");
     } else{ // 如果是其他对话框，则直接关掉弹窗
//      confirm.ok();
     confirm.quit();
     System.out.println(Thread.currentThread().getName()
     + "--->关掉对话框。");
     }
ret = true; // 处理完弹窗，设置“结束等待”标志
}
}
}else{
ret = true; // IE已不存在，设置“结束等待”标志
}
return ret;
}
     }
// 设置等待时间
final WaitReady waitReady = new WaitReady(TIMEOUT_POPUP, WAIT_INCREMENT);
waitReady.waitUntil( new DialogReady() );
}

}catch(Exception e){
System.println(e + getClass().getName()+ "handlePopupDialog()");
}
}
}).start();
  }
     
  public class WaitReady {
long timeout;
long waitIncrement;
     Timer timer;
     public WaitReady(long paramTimeout, long paramWaitIncrement) {
     timeout = paramTimeout;
     waitIncrement = paramWaitIncrement;
     }
     public boolean waitUntil(MyReady ready) throws Exception {
         timer = new TimerImpl();
         while (!ready.isReady()) {
             if (timer.isElapsed(timeout)) {
             System.out.println("Timeout exceeded.  Reason= " + ready.getNotReadyReason());
             actionAfterTimeout();
             return false;
             }
             try {
//              controller.handleOutput("  线程：" + Thread.currentThread().getName() + "正在等待");
             Thread.sleep(waitIncrement);
             ready.actionWhenWait();
             } catch (InterruptedException e) {
             throw new TimeException(e);
             }
         }
         return true;
     }
     public void actionAfterTimeout() throws Exception {
     
     }
     public Timer getTimer(){
     return timer;
     }
}

  public interface MyReady {
     boolean isReady() throws Exception;

     String getNotReadyReason();

     boolean actionWhenWait() throws Exception;
  }
  
}

{% endhighlight %}

目前，经过我修改的Watij还是存在一些不足，由于弹窗标题和按钮文字都被固化在程序里，如果遇到其他特殊的弹窗就无法处理了。希望今后能够增强Watij的灵活性，使得不管是哪个版本的IE，什么样的弹窗都能处理。
