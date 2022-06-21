---
layout: post
title: loadrunner 监控Tomcat
categories: loadrunner
tags: tomcat
---

## 通过JConsole监控Tomcat 

1、打开tomcat5的bin目录中的catalina.bat文件，在头部注释部分的后面加上：

set JAVA_OPTS=%JAVA_OPTS% -Dcom.sun.management.jmxremote.port=8999 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false  -Xms1024m -Xmx1024m  -XX:PermSize=128M -XX:PermSize=256M

（如果用eclipse启动tomcat，则在Window-->Preferences-->Tomcat-->JVM Setting 第一个框内Add两行：

    1、-Xms1024m -Xmx1024m  -XX:PermSize=128M -XX:PermSize=256M ：设置tomcat启动内存
    2、-Dcom.sun.management.jmxremote.port=8999 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false：设置jconsole远程连接tomcat
    3、tomcat登录用户在tomcat的conf下tomcat-users.xml中：，给tomcat用户manager权限，这样在tomcat页面就可以用tomcat/tomcat登录了，jconsole中远程连接时也可以用这个用户登录）


2、修改JMX远程访问授权。

进入JAVA安装目录的\jre6\lib\management目录，把jmxremote.password.template文件改名为jmxremote.password，去掉最后两行的注释（用户名和密码）：

    monitorRole  QED

    controlRole   R&D

确保jmxremote.access文件末尾的访问角色没有被注释掉：

    monitorRole   readonly

    controlRole   readwrite \

    create javax.management.monitor.*,javax.management.timer.* \

    unregister

然后分别选择这两个文件，点右键“属性”-〉安全，点“高级”，去掉“从父项继承....”，弹出窗口中选“删除”，这样删除了所有访问权限。 再选“添加”-〉高级，“立即查找”，选中你的用户，例administrator，点“确定"，“确定"。来到权限窗口，勾选"完全控制"，点"确定"

注：JDK好像需要装在NTFS文件系统下才行

3、启动Tomcat

4、在命令行输入netstat -an 查看端口8999是否已经打开，如果没有，则是前面的配置没配好。

5、如果已经配置好，则在命令行输入jconsole，打开jdk自带的JMX客户端，选择远程连接，录入tomcat所在机器的IP，端口例 192.168.1.100:8999，帐号、密码在jmxremote.password中，如帐号controlRole，密码R&D（缺省 monitorRole只能读，controlRole能读写，jmxremote.access中可配置）。点“连接”。

编写JAVA程序收集Tomcat性能数据

{% highlight java %} 

import java.lang.management.MemoryUsage;
 import java.text.SimpleDateFormat;
 import java.util.Date;
 import java.util.Formatter;
 import java.util.HashMap;
 import java.util.Iterator;
 import java.util.Map;
 import java.util.Set;
 import javax.management.MBeanAttributeInfo;
 import javax.management.MBeanInfo;
 import javax.management.MBeanServerConnection;
 import javax.management.ObjectInstance;
 import javax.management.ObjectName;
 import javax.management.openmbean.CompositeDataSupport;
 import javax.management.remote.JMXConnector;
 import javax.management.remote.JMXConnectorFactory;
 import javax.management.remote.JMXServiceURL;
  
 public class MonitorTomcat { 
  
      public static void main(String[] args) { 
         try { 
  
            String jmxURL = "service:jmx:rmi:///jndi/rmi://192.168.1.100:8999/jmxrmi";//tomcat jmx url 
            JMXServiceURL serviceURL = new JMXServiceURL(jmxURL); 
             
            Map map = new HashMap(); 
            String[] credentials = new String[] { "monitorRole" , "QED" }; 
            map.put("jmx.remote.credentials", credentials);  
            JMXConnector connector = JMXConnectorFactory.connect(serviceURL, map); 
            MBeanServerConnection  mbsc = connector.getMBeanServerConnection(); 
             
            //端口最好是动态取得 
            ObjectName threadObjName = new ObjectName("Catalina:type=ThreadPool,name=http-8080"); 
            MBeanInfo mbInfo = mbsc.getMBeanInfo(threadObjName); 
             
            String attrName = "currentThreadCount";//tomcat的线程数对应的属性值 
            MBeanAttributeInfo[] mbAttributes = mbInfo.getAttributes(); 
            System.out.println("currentThreadCount:"+mbsc.getAttribute(threadObjName, attrName)); 
             
            //heap 
            for(int j=0;j
                System.out.println("###########"+mbsc.getDomains()[j]);  
            }  
            Set MBeanset = mbsc.queryMBeans(null, null); 
            System.out.println("MBeanset.size() : " + MBeanset.size()); 
            Iterator MBeansetIterator = MBeanset.iterator(); 
            while (MBeansetIterator.hasNext()) {  
                ObjectInstance objectInstance = (ObjectInstance)MBeansetIterator.next(); 
                ObjectName objectName = objectInstance.getObjectName(); 
                String canonicalName = objectName.getCanonicalName(); 
                System.out.println("canonicalName : " + canonicalName);  
                if (canonicalName.equals("Catalina:host=localhost,type=Cluster"))      {   
                    // Get details of cluster MBeans 
                    System.out.println("Cluster MBeans Details:"); 
                    System.out.println("=========================================");  
                    //getMBeansDetails(canonicalName); 
                   String canonicalKeyPropList = objectName.getCanonicalKeyPropertyListString(); 
               } 
            } 
            //------------------------- system ---------------------- 
            ObjectName runtimeObjName = new ObjectName("java.lang:type=Runtime"); 
            System.out.println("厂商:"+ (String)mbsc.getAttribute(runtimeObjName, "VmVendor")); 
            System.out.println("程序:"+ (String)mbsc.getAttribute(runtimeObjName, "VmName")); 
            System.out.println("版本:"+ (String)mbsc.getAttribute(runtimeObjName, "VmVersion")); 
            Date starttime=new Date((Long)mbsc.getAttribute(runtimeObjName, "StartTime")); 
            SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); 
            System.out.println("启动时间:"+df.format(starttime)); 
             
            Long timespan=(Long)mbsc.getAttribute(runtimeObjName, "Uptime"); 
            System.out.println("连续工作时间:"+MonitorTomcat.formatTimeSpan(timespan)); 
            //------------------------ JVM ------------------------- 
            //堆使用率 
            ObjectName heapObjName = new ObjectName("java.lang:type=Memory"); 
            MemoryUsage heapMemoryUsage =  MemoryUsage.from((CompositeDataSupport)mbsc.getAttribute(heapObjName, "HeapMemoryUsage")); 
            long maxMemory = heapMemoryUsage.getMax();//堆最大 
            long commitMemory = heapMemoryUsage.getCommitted();//堆当前分配 
            long usedMemory = heapMemoryUsage.getUsed(); 
            System.out.println("heap:"+(double)usedMemory*100/commitMemory+"%");//堆使用率 
             
            MemoryUsage nonheapMemoryUsage =  MemoryUsage.from((CompositeDataSupport)mbsc.getAttribute(heapObjName, "NonHeapMemoryUsage"));            
            long noncommitMemory = nonheapMemoryUsage.getCommitted(); 
            long nonusedMemory = heapMemoryUsage.getUsed(); 
            System.out.println("nonheap:"+(double)nonusedMemory*100/noncommitMemory+"%"); 
             
            ObjectName permObjName = new ObjectName("java.lang:type=MemoryPool,name=Perm Gen"); 
            MemoryUsage permGenUsage =  MemoryUsage.from((CompositeDataSupport)mbsc.getAttribute(permObjName, "Usage"));            
            long committed = permGenUsage.getCommitted();//持久堆大小 
            long used = heapMemoryUsage.getUsed();// 
            System.out.println("perm gen:"+(double)used*100/committed+"%");//持久堆使用率 
             
            //-------------------- Session ---------------  
            ObjectName managerObjName = new ObjectName("Catalina:type=Manager,*"); 
            Set s=mbsc.queryNames(managerObjName, null); 
            for (ObjectName obj:s){ 
                System.out.println("应用名:"+obj.getKeyProperty("path")); 
                ObjectName objname=new ObjectName(obj.getCanonicalName()); 
                System.out.println("最大会话数:"+ mbsc.getAttribute( objname, "maxActiveSessions")); 
                System.out.println("会话数:"+ mbsc.getAttribute( objname, "activeSessions")); 
                System.out.println("活动会话数:"+ mbsc.getAttribute( objname, "sessionCounter")); 
            } 
             
            //----------------- Thread Pool ---------------- 
            ObjectName threadpoolObjName = new ObjectName("Catalina:type=ThreadPool,*"); 
            Set s2=mbsc.queryNames(threadpoolObjName, null); 
            for (ObjectName obj:s2){ 
                System.out.println("端口名:"+obj.getKeyProperty("name")); 
                ObjectName objname=new ObjectName(obj.getCanonicalName()); 
                System.out.println("最大线程数:"+ mbsc.getAttribute( objname, "maxThreads")); 
                System.out.println("当前线程数:"+ mbsc.getAttribute( objname, "currentThreadCount")); 
                System.out.println("繁忙线程数:"+ mbsc.getAttribute( objname, "currentThreadsBusy")); 
            } 
                 
         } catch (Exception e) { 
             e.printStackTrace(); 
         } 
     } 
  
     public static String formatTimeSpan(long span){ 
         long minseconds = span % 1000; 
          
         span = span /1000; 
         long seconds = span % 60; 
          
         span = span / 60; 
         long mins = span % 60; 
          
         span = span / 60; 
         long hours = span % 24; 
  
         span = span / 24; 
         long days = span; 
         return (new Formatter()).format("%1$d天 %2$02d:%3$02d:%4$02d.%5$03d", days,hours,mins,seconds,minseconds).toString(); 
     } 
 } 

 {% endhighlight %}

记录的数据：

　　currentThreadCount:150

　　###########JMImplementation

　　###########Users

　　###########com.sun.management

　　###########Catalina

　　###########java.lang

　　###########java.util.logging

　　MBeanset.size() : 383

　　canonicalName : Catalina:name=HttpRequest152,type=RequestProcessor,worker=http-8080

　　canonicalName : Catalina:J2EEApplication=none,J2EEServer=none,WebModule=//localhost/jsp-examples,j2eeType=Servlet,name=org.apache.jsp.jsp2.el.basic_002dcomparisons_jsp

　　……

　　canonicalName : Catalina:port=8009,type=Mapper

　　canonicalName : Catalina:J2EEApplication=none,J2EEServer=none,WebModule=//localhost/jsp-examples,j2eeType=Servlet,name=org.apache.jsp.dates.date_jsp

　　厂商:Sun Microsystems Inc.

　　程序:Java HotSpot(TM) Client VM

　　版本:11.3-b02

　　启动时间:2010-02-09 11:36:59

　　连续工作时间:0天 00:13:55.775

　　heap:62.221410820735%

　　nonheap:84.59782727899399%

　　perm gen:210.93058268229166%

　　应用名:/jsp-examples

　　最大会话数:-1

　　会话数:0

　　活动会话数:0

　　应用名:/

　　最大会话数:-1

　　会话数:0

　　活动会话数:0

　　应用名:/host-manager

　　最大会话数:-1

　　会话数:0

　　活动会话数:0

　　应用名:/tomcat-docs

　　最大会话数:-1

　　会话数:0

　　活动会话数:0

　　应用名:/AltoroJ

　　最大会话数:-1

　　会话数:301

　　活动会话数:301

　　应用名:/webdav

　　最大会话数:-1

　　会话数:0

　　活动会话数:0

　　应用名:/servlets-examples

　　最大会话数:-1

　　会话数:0

　　活动会话数:0

　　应用名:/manager

　　最大会话数:-1

　　会话数:0

　　活动会话数:0

　　应用名:/balancer

　　最大会话数:-1

　　会话数:0

　　活动会话数:0

　　端口名:http-8080

　　最大线程数:150

　　当前线程数:150

　　繁忙线程数:100

　　端口名:jk-8009

　　最大线程数:200

　　当前线程数:4

　　繁忙线程数:1

　　使用LR的lr_user_data_point函数
 
",

               "Ord=1",
               LAST);
  
        web_reg_save_param("ServerJVMVersion",
               "LB=",
               "Ord=2",
               LAST);
  
        web_reg_save_param("ServerJVMVendor",
               "LB=",
               "Ord=3",
               LAST);
  
        web_reg_save_param("ServerOSName",
               "LB=",
               "Ord=4",
               LAST);
  
        web_reg_save_param("ServerOSVersione",
               "LB=",
               "Ord=5",
               LAST);
  
        web_reg_save_param("ServerOSArchitecture",
               "LB=",
               "Ord=6",
               LAST);
  
          web_reg_find("Text=/manager",
               LAST);
  
        web_url("status",
               "URL=http://{ServerName}/manager/status",
               "Resource=0",
               "RecContentType=text/html",
               "Referer=",
               "Snapshot=t1.inf",
               "Mode=HTTP",
               LAST);
  
        lr_end_transaction("monitor tomcat", LR_AUTO);
  
        // Tomcat JVM metrics
        lr_user_data_point("Tomcat JVM Free memory", atof(lr_eval_string("{JVMFreeMemory}")));
        lr_user_data_point("Tomcat JVM Total memory", atof(lr_eval_string("{JVMTotalMemory}")));
        lr_user_data_point("Tomcat JVM Max memory", atof(lr_eval_string("{JVMMaxMemory}")));
  
      
        // Tomcat web server metrics
        lr_user_data_point("Tomcat HTTP Max threads", atof(lr_eval_string("{HTTPMaxThreads}")));
        //lr_user_data_point("Tomcat HTTP Min spare threads", atof(lr_eval_string("{HTTPMinSpareThreads}")));
        //lr_user_data_point("Tomcat HTTP Max spare threads", atof(lr_eval_string("{HTTPMaxSpareThreads}")));
        lr_user_data_point("Tomcat HTTP Current spare threads", atof(lr_eval_string("{HTTPCurrentSpareThreads}")));
        lr_user_data_point("Tomcat HTTP Current thread busy", atof(lr_eval_string("{HTTPCurrentThreadBusy}")));
        lr_user_data_point("Tomcat HTTP Max processing time", atof(lr_eval_string("{HTTPMaxProcessingTime}")));
        lr_user_data_point("Tomcat HTTP Request count", atof(lr_eval_string("{HTTPRequestCount}")));
        lr_user_data_point("Tomcat HTTP Error count", atof(lr_eval_string("{HTTPErrorCount}")));
        lr_user_data_point("Tomcat HTTP Bytes received", atof(lr_eval_string("{HTTPBytesReceived}")));
        lr_user_data_point("Tomcat HTTP Bytes sent", atof(lr_eval_string("{HTTPBytesSent}")));
      
      
        // Tomcat servlet container metrics
        lr_user_data_point("Tomcat JK Max threads", atof(lr_eval_string("{JKMaxThreads}")));
        lr_user_data_point("Tomcat JK Min spare threads", atof(lr_eval_string("{JKMinSpareThreads}")));
        lr_user_data_point("Tomcat JK Max spare threads", atof(lr_eval_string("{JKMaxSpareThreads}")));
        lr_user_data_point("Tomcat JK Current spare threads", atof(lr_eval_string("{JKCurrentSpareThreads}")));
        lr_user_data_point("Tomcat JK Current thread busy", atof(lr_eval_string("{JKCurrentThreadBusy}")));
        lr_user_data_point("Tomcat JK Max processing time", atof(lr_eval_string("{JKMaxProcessingTime}")));
        lr_user_data_point("Tomcat JK Request count", atof(lr_eval_string("{JKRequestCount}")));
        lr_user_data_point("Tomcat JK Error count", atof(lr_eval_string("{JKErrorCount}")));
        lr_user_data_point("Tomcat JK Bytes received", atof(lr_eval_string("{JKBytesReceived}")));
        lr_user_data_point("Tomcat JK Bytes sent", atof(lr_eval_string("{JKBytesSent}")));
      
  
        // Determine number of threads in each state.
        // Note that there is some complexity in doing this (see code below)...
        // P: Parse and prepare request S: Service F: Finishing R: Ready K: Keepalive
  
     if (strcmp(lr_eval_string("{StageTable_count}"), "2") != 0) { // Verify that there are only two tables.
               lr_error_message("Monitoring script expected 2 tables of thread metrics, but instead found %s", lr_eval_string("{StageTable_count}"));
        } else {   // extract the P, S, F, R, and K values from both the tables.
  
               // check that there are only 2 tables. Raise an error if there is not...
               //lr_save_searched_string(const char *buffer, long buf_size, unsigned int occurrence, const char *search_string, int offset, unsigned int string_len, const char *parm_name );
               // maybe should use strtok for this.
               //strtok
               // lr_xml_get_values
         lr_output_message("%s", lr_eval_string("{StageTable_1}"));
               numValues= lr_xml_get_values("XML={StageTable_1}",
                      "ValueParam=OutputParam",
                      "Query=/tr/td/strong/*",
                      "SelectAll=yes", LAST);
  
        }
  
  
  
        // Log Tomcat/JVM version info on the first iteration only.
        if (loggedVersionInfo != TRUE) {
               lr_log_message("****** Test Environment Information ******");
               lr_log_message("%s", lr_eval_string("Tomcat: {ServerTomcatVersion}"));
               lr_log_message("%s", lr_eval_string("JVM: {ServerJVMVersion} {ServerJVMVendor}"));
               lr_log_message("%s", lr_eval_string("Operating System: {ServerOSName} {ServerOSVersione} {ServerOSArchitecture}"));
               lr_log_message("******************************************");
               loggedVersionInfo = TRUE;
        }
      
  
        return 0;
    } 

// This script collects server metrics from the Tomcat Status page (http://127.0.0.1:8080/manager/status).
 // Runtime settings are set to run this script once every 5 seconds.
  
     double atof (const char *string);
     extern char* strtok(char *token, const char *delimiter);
      
     CollectMetrics()
     {
        int countP, countS, countF, countR, countK;
        int numValues;
        static int loggedVersionInfo = FALSE;
  
        lr_save_string("127.0.0.1:8080", "ServerName");
        web_set_max_html_param_len("102480"); // 65536 Note: this may need to be increased.
  
        web_set_user("admin",
               "123456",
               "{ServerName}");
  
        lr_start_transaction("monitor tomcat");
  
       
  
        web_reg_save_param("JVMFreeMemory",
               "LB=Free memory: ",
               "RB= MB",
               "Ord=1",
               LAST);
  
        web_reg_save_param("JVMTotalMemory",
               "LB=Total memory: ",
               "RB= MB",
               "Ord=1",
               LAST);
  
        web_reg_save_param("JVMMaxMemory",
               "LB=Max memory: ",
               "RB= MB",
               "Ord=1",
               LAST);
      
        web_reg_save_param("HTTPMaxThreads",
               "LB=Max threads: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPMinSpareThreads",
               "LB=Min spare threads: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPMaxSpareThreads",
               "LB=Max spare threads: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPCurrentSpareThreads",
               "LB=Current thread count: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPCurrentThreadBusy",
               "LB=Current thread busy: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPMaxProcessingTime",
               "LB=Max processing time: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPRequestCount",
               "LB=Request count: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPErrorCount",
               "LB=Error count: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPBytesReceived",
               "LB=Bytes received: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("HTTPBytesSent",
               "LB=Bytes sent: ",
               "RB= ",
               "Ord=1",
               LAST);
  
        web_reg_save_param("JKMaxThreads",
               "LB=Max threads: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKMinSpareThreads",
               "LB=Min spare threads: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKMaxSpareThreads",
               "LB=Max spare threads: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKCurrentSpareThreads",
               "LB=Current thread count: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKCurrentThreadBusy",
               "LB=Current thread busy: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKMaxProcessingTime",
               "LB=Max processing time: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKRequestCount",
               "LB=Request count: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKErrorCount",
               "LB=Error count: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKBytesReceived",
               "LB=Bytes received: ",
               "RB= ",
               "Ord=2",
               LAST);
  
        web_reg_save_param("JKBytesSent",
               "LB=Bytes sent: ",
               "RB= ",
               "Ord=2",
               LAST);
   
        // Version Information
         
        web_reg_save_param("ServerTomcatVersion",
               "LB= ",
               "RB= ",
               "RB= ",
               "RB= ",
               "RB= ",
               "RB= ",
               "RB= 

使用SiteScope监控Tomcat

1、安装SiteScope

    LR9.5的安装包中附带了SiteScope9.5的安装文件

    \Additional Components\Sitescope\SiteScope 9.50

2、配置SiteScope

    新建监视器，选择JMX类别

    输入Tomcat的JMX URL地址，例如：

    service:jmx:rmi:///jndi/rmi://192.168.1.100:8999/jmxrmi

    用户名：monitorRole

    密码：QED

    添加计数器，例如：

    java.lang/Memory/HeapMemoryUsage/used

    java.lang/Memory/HeapMemoryUsage/max

    Catalina/ThreadPool/jk-8010/currentThreadCount

    Catalina/ThreadPool/jk-8010/maxSpareThreads

    Catalina/ThreadPool/jk-8010/minSpareThreads

    然后就可以在LR的Controller中连接SiteScope对Tomcat进行监控。
