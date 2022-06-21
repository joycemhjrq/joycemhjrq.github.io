---
layout: post
title: Android 调用打电话和发短信、收短信接口、发Email 
categories: Android
tags: 
    - phonecall
    - message
    - email
---

[原文]<http://blog.csdn.net/xjanker2/article/details/6074344>

打电话和发短信可以说是最核心的应用了，本文就来阐述它的调用方法。可以分为直接调用--直接电话或短信发出，已经间接调用--进入拨号或短信撰写页面，等待用户确认内容后由用户发出.

先看代码效果截图：

<img src="/media/img/android-call.jpg">

先编写主界面Activaty，创建类CallAndSms作为为默认启动页

[java] view plaincopy

{% highlight java %}

package jtapp.callandsms;  
  
import java.util.List;  
  
import android.app.Activity;  
import android.content.Intent;  
import android.net.Uri;  
import android.os.Bundle;  
import android.telephony.SmsManager;  
import android.view.View;  
import android.view.View.OnClickListener;  
import android.widget.Button;  
import android.widget.Toast;  
  
public class CallAndSms extends Activity {  
      
    @Override  
    public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.main);  
         
        setComponent();  
    }  
      
    private void setComponent() {  
        Button bt1 = (Button) findViewById(R.id.Button01);  
        bt1.setOnClickListener(new OnClickListener() {  
            @Override  
            public void onClick(View v) {  
                Intent intent = new Intent(  
                        Intent.ACTION_CALL, Uri.parse("tel:10010"));  
                startActivity(intent);  
            }  
        });  
        Button bt2 = (Button) findViewById(R.id.Button02);  
        bt2.setOnClickListener(new OnClickListener() {  
            @Override  
            public void onClick(View v) {  
                String smsContent = "102";  
                // note: SMS must be divided before being sent    
                SmsManager sms = SmsManager.getDefault();  
                List texts = sms.divideMessage(smsContent);  
                for (String text : texts) {  
                    sms.sendTextMessage("10010", null, text, null, null);  
                }  
                // note: not checked success or failure yet  
                Toast.makeText(  
                        CallAndSms.this,   
                        "短信已发送",  
                        Toast.LENGTH_SHORT ).show();  
            }  
        });  
          
        Button bt3 = (Button) findViewById(R.id.Button03);  
        bt3.setOnClickListener(new OnClickListener() {  
            @Override  
            public void onClick(View v) {  
                Intent intent = new Intent(  
                        Intent.ACTION_DIAL, Uri.parse("tel:10010"));  
                startActivity(intent);  
            }  
        });  
        Button bt4 = (Button) findViewById(R.id.Button04);  
        bt4.setOnClickListener(new OnClickListener() {  
            @Override  
            public void onClick(View v) {  
                Uri uri = Uri.parse("smsto:10010");          
                Intent it = new Intent(Intent.ACTION_SENDTO, uri);          
                it.putExtra("sms_body", "102");          
                startActivity(it);   
            }  
        });  
    }  
} 

{% endhighlight %} 

主界面ui定义 main.xml 代码:


[xhtml] view plaincopy

	<?xml version="1.0" encoding="utf-8"?>  
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
	    android:orientation="vertical" android:layout_width="fill_parent"  
	    android:layout_height="fill_parent" android:gravity="center">  
	    <TextView android:layout_width="fill_parent"  
	        android:layout_height="wrap_content" android:text="Direct Method:"  
	        android:gravity="center" />  
	    <Button android:text="拨打10010客服电话" android:id="@+id/Button01"  
	        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>  
	    <Button android:text="短信10010查余额" android:id="@+id/Button02"  
	        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>  
	    <TextView android:text="InDirect Method:" android:id="@+id/TextView01"  
	        android:layout_width="wrap_content" android:layout_height="wrap_content" />  
	    <Button android:text="拨打10010客服电话" android:id="@+id/Button03"  
	        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>  
	    <Button android:text="短信10010查余额" android:id="@+id/Button04"  
	        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>  
	</LinearLayout>  

用于监听短信到来的Broadcast消息的文件，

ReceiverSMS.java 代码：

[java] view plaincopy

{% highlight java %}

package jtapp.callandsms;  
  
import android.content.BroadcastReceiver;  
import android.content.Context;  
import android.content.Intent;  
import android.os.Bundle;  
import android.telephony.SmsMessage;  
import android.widget.Toast;  
  
public class ReceiverSMS extends BroadcastReceiver {  
  
    @Override  
    public void onReceive(Context context, Intent intent) {  
        if (intent.getAction().equals(  
                "android.provider.Telephony.SMS_RECEIVED")) {  
            StringBuilder sb = new StringBuilder();  
            Bundle bundle = intent.getExtras();  
            if (bundle != null) {  
                Object[] pdus = (Object[]) bundle.get("pdus");  
                SmsMessage[] msgs = new SmsMessage[pdus.length];  
                for (int i = 0; i < pdus.length; i++) {  
                    msgs[i] = SmsMessage  
                        .createFromPdu((byte[]) pdus[i]);  
                }  
                for (SmsMessage s : msgs) {  
                    sb.append("收到来自");  
                    sb.append(s.getDisplayOriginatingAddress());  
                    sb.append("的SMS, 内容:");  
                    sb.append(s.getDisplayMessageBody());  
                }  
                Toast.makeText(  
                        context,   
                        "收到了短消息: " + sb.toString(),  
                        Toast.LENGTH_LONG).show();  
            }  
          
        }  
    }  
}  

{% endhighlight %}

AndroidManifest.xml中权限、activity和receiver的设定：

[java] view plaincopy

	<?xml version="1.0" encoding="utf-8"?>  
	<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
	    package="jtapp.callandsms" android:versionCode="1" android:versionName="1.0">  
	    <application android:icon="@drawable/icon" android:label="@string/app_name"  
	        android:debuggable="true">  
	        <activity android:name=".CallAndSms" android:label="@string/app_name">  
	            <intent-filter>  
	                <action android:name="android.intent.action.MAIN" />  
	                <category android:name="android.intent.category.LAUNCHER" />  
	            </intent-filter>  
	        </activity>  
	        <receiver android:name=".ReceiverSMS" android:enabled="true">  
	            <intent-filter>  
	                <action android:name="android.provider.Telephony.SMS_RECEIVED" />  
	            </intent-filter>  
	        </receiver>  
	    </application>  
	    <uses-permission android:name="android.permission.CALL_PHONE"></uses-permission>  
	    <uses-permission android:name="android.permission.SEND_SMS"></uses-permission>  
	    <uses-permission android:name="android.permission.RECEIVE_SMS"></uses-permission>  
	</manifest>  

补充，发Email 代码片段：

[java] view plaincopy

{% highlight java %}

//Email  
Button bt5 = (Button) findViewById(R.id.Button05);  
bt5.setOnClickListener(new OnClickListener() {  
    @Override  
    public void onClick(View v) {  
        // Setup the recipient in a String array  
        String[] mailto = { "noam@gmail.com" };  
        // Create a new Intent to send messages  
        Intent sendIntent = new Intent(Intent.ACTION_SEND);  
        // Write the body of theEmail  
        String emailBody = "You're password is: ";  
        // Add attributes to the intent  
        //sendIntent.setType("text/plain"); // use this line for testing  
                                            // in the emulator  
        sendIntent.setType("message/rfc822"); // use this line for testing   
                                                // on the real phone  
        sendIntent.putExtra(Intent.EXTRA_EMAIL, mailto);  
        sendIntent.putExtra(Intent.EXTRA_SUBJECT, "Your Password");  
        sendIntent.putExtra(Intent.EXTRA_TEXT, emailBody);  
        startActivity(sendIntent);  
    }  
}); 

{% endhighlight %}