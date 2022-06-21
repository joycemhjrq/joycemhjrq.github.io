---
layout: post
title: loadrunner 关联实战
categories: loadrunner
tags: correlation
---

loadrunner 关联有三个函数：

web_reg_save_param、web_create_html_param、web_create_html_param_ex

## web_reg_save_param：这是最新版，也是最常用来做关联（correlation）的函数。

语法：

web_reg_save_param ( “Parameter Name” , < list of Attributes >, LAST );

web_create_html_param、web_create_html_param_ex：这二个函数主要是保留作为向前兼容的目的的。建议使用web_reg_save_param函数。

手动关联的执行过程大致如下：

1、使用相同的业务流程与数据，录制二份脚本

2、使用loadrunner：Tools-->Compare with Script可找出需要关联的数据。

脚本如下：

    Action()
     {

         web_set_max_html_param_len("1024");

        

         web_reg_save_param("JSESSIONID4",
            "LB/IC=jsessionid=",
            "RB/IC="",
            "Ord=1",
            "RelFrameId=1",
            "Search=body",
            LAST);

        web_create_html_param("lt",
            "name="lt" value="",
            """);

         web_url("login",
             "URL=https://cas:8338/cas/login",
             "Resource=0",
             "RecContentType=text/html",
             "Referer=",
             "Snapshot=t10.inf",
             "Mode=HTML",
             EXTRARES,
             "URL=theme/red/css/cssreset/reset-min.css", ENDITEM,
             "URL=theme/red/jq/accordion/gh.accordion.css", ENDITEM,
             "URL=theme/red/css/cssfonts/fonts-min.css", ENDITEM,
             "URL=theme/red/css/cssbase/base-min.css", ENDITEM,
             "URL=theme/red/jq/keypad.1.2.2/jquery.keypad.css", ENDITEM,
             "URL=theme/red/img/sprite_bd_btn.png", ENDITEM,
             "URL=theme/red/img/sprite_pay_icon.png", ENDITEM,
             "URL=https://ssl.google-analytics.com/ga.js", ENDITEM,
             "URL=theme/red/jq/tabs/tab.png", ENDITEM,
             "URL=theme/red/jq/keypad.1.2.2/keypad.png", ENDITEM,
             LAST);

         web_custom_request("urs.asmx",
             "URL=https://urs.microsoft.com/urs.asmx?MSPRU-Client-Key=q92TLcWWCOrCs2KtbnLAVg==&MSPRU-Patented-Lock=US5/Mh+o8nQ=",
             "Method=POST",
             "Resource=0",
             "RecContentType=text/xml",
             "Referer=",
             "Snapshot=t11.inf",
             "Mode=HTML",
             "EncType=text/xml; charset=utf-8",
             "Body=<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/"><soap:Body><Lookup xmlns="http://Microsoft.STS.STSWeb/"><r soapenc:arrayType="xsd:string[1]"><string>https://cas/cas/login;jsessionid=ac6396fd77ea9b2074281d3072bb659a</string></r><ID>{A0AB7674-8D67-4F4D-B5E1-96FAEADFB79D}</ID><v soapenc:arrayType="xsd:string[5]"><string>7.0.6004.6</string><string>7.00.6000.16730</string><string>7.0.5730.13</string><string>5.1.2600.3.0</string><string>zh-cn</string></v></Lookup></soap:Body></soap:Envelope>",
             LAST);

      
         web_submit_data("login;jsessionid=D0C07C450E041F4EB71C9290570E05B3",
             "Action=https://cas:8338/cas/login;jsessionid={JSESSIONID4}",
             "Method=POST",
             "RecContentType=text/html",
             "Referer=https://cas:8338/cas/login",
             "Snapshot=t12.inf",
             "Mode=HTML",
             ITEMDATA,
             "Name=username", "Value=a@a.com", ENDITEM,
             "Name=password", "Value=a", ENDITEM,
             "Name=imageCode", "Value=", ENDITEM,
             "Name=submit", "Value=", ENDITEM,
             "Name=warn", "Value=false", ENDITEM,
             "Name=lt", "Value={lt}", ENDITEM,
             "Name=_eventId", "Value=submit", ENDITEM,
             LAST);
         web_find("web_find",
             "What=您已经成功登录了！",
             LAST);
        
         web_url("LogAccessInfo.html",
             "URL=http://ad.haowangjiao.com/LogAccessInfo.html?fromUrl=https://cas:8338/cas/login&keyword=&sourceUrl=https://cas:8338/cas/login&uuid=E561D54B-4FBF-4915-A2A0-9E8A5DA2A8B6&linkId=-1",
             "Resource=0",
             "RecContentType=text/html",
             "Referer=",
             "Snapshot=t13.inf",
             "Mode=HTML",
             LAST);

         return 0;
     }

这里要注意关联函数存放的位置，它不是直接放在web_submit_data的前面，而是放在了整个文件的最前面，如果直接放到web_submit_data前面的话，会报500的错误，还有 web_submit_data("login;jsessionid=D0C07C450E041F4EB71C9290570E05B3","Action=https://cas:8338/cas/login;jsessionid={JSESSIONID4}",第一个jsessionid和第二个jsessionid的处理方法不一样：（也不太清楚为什么，可能是第一个的位置无法从关联中取得值吧，因为如果第一个jsessionid也用关联的话，从log中看，它还是jsessionid=JSESSIONID4｝的形式）

 从log中可以看到关联的值为多少，下面是Replay log：

    Virtual User Script started
    Starting action vuser_init.
    Web Turbo Replay of LoadRunner 9.0.0 for WINXP; WebReplay82 build 5727      [MsgId: MMSG-27143]
    Run-Time Settings file: "C:\Documents and Settings\Administrator\×ÀÃæ\c\\default.cfg"      [MsgId: MMSG-27141]
    Ending action vuser_init.
    Running Vuser...
    Starting iteration 1.
    Starting action Action.
    Action.c(4): web_set_max_html_param_len was successful      [MsgId: MMSG-26392]
    Action.c(10): Registering web_reg_save_param was successful      [MsgId: MMSG-26390]
    Action.c(18): Registering web_create_html_param was successful      [MsgId: MMSG-26390]
    Action.c(22): Notify: Saving Parameter "JSESSIONID4 = 93116597F9263946ADCFD7AA3BA34555"
    Action.c(22): Notify: Saving Parameter "lt = _c68864BC7-FF33-9B55-CEC2-180473614BB9_k6BAF7C2C-AAE9-649E-1A1B-014BD23D3D94"
    Action.c(22): Found resource "https://cas:8338/cas/theme/red/css/userpage_import.css" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/theme/red/jq/jquery-1.3.2.min.js" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/theme/red/jq/tabs/ui.tabs.css" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/theme/red/jq/tabs/ui.tabs.pack.js" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/theme/red/jq/keypad.1.2.2/jquery.keypad.js" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/theme/red/css/userpage_spec.css" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/theme/red/css/layout.css" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/theme/red/img/logo.gif" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/imageCode" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Detected non-resource "https://dev.pt.sdo.com/LoginFrame.asp?returnURL=https://cas:8338/cas/sdoUserCheck.html?service=null&appDomain=cas&curURL=https://cas:8338/?service=null&CSSURL=https://cas:8338/cas/theme/red/css/login_iframe_cas.css" in "https://cas:8338/cas/login"      [MsgId: MMSG-26574]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/css/cssreset/reset-min.css" (specified by argument number 9)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/jq/accordion/gh.accordion.css" (specified by argument number 11)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/css/cssfonts/fonts-min.css" (specified by argument number 13)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/css/cssbase/base-min.css" (specified by argument number 15)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/jq/keypad.1.2.2/jquery.keypad.css" (specified by argument number 17)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/img/sprite_bd_btn.png" (specified by argument number 19)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/img/sprite_pay_icon.png" (specified by argument number 21)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://ssl.google-analytics.com/ga.js" (specified by argument number 23)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/jq/tabs/tab.png" (specified by argument number 25)      [MsgId: MMSG-26577]
    Action.c(22): Downloading resource "https://cas:8338/cas/theme/red/jq/keypad.1.2.2/keypad.png" (specified by argument number 27)      [MsgId: MMSG-26577]
    Action.c(22): Found resource "https://cas:8338/cas/js/adaliance.js" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Found resource "https://cas:8338/cas/js/googleNewGaJsHost.js" in HTML "https://cas:8338/cas/login"      [MsgId: MMSG-26659]
    Action.c(22): Continuing after Error -27796: Failed to connect to server "dev.pt.sdo.com:443": [10061] Connection refused      [MsgId: MERR-27796]
    Action.c(22): Warning -26000: Empty or no response for URL="https://dev.pt.sdo.com/LoginFrame.asp?returnURL=https://cas:8338/cas/sdoUserCheck.html?service=null&appDomain=cas&curURL=https://cas:8338/?service=null&CSSURL=https://cas:8338/cas/theme/red/css/login_iframe_cas.css"      [MsgId: MWAR-26000]
    Action.c(22): web_url("login") highest severity level was "continue on error", 358981 body bytes, 4868 header bytes, 32 chunking overhead bytes      [MsgId: MMSG-26387]
    Action.c(22): Continuing after error in Vuser script.
    Action.c(42): Warning: The string 'A0AB7674-8D67-4F4D-B5E1-96FAEADFB79D' with parameter delimiters is not a parameter.
    Action.c(42): web_custom_request("urs.asmx") was successful, 1128 body bytes, 279 header bytes      [MsgId: MMSG-26386]
    Action.c(55): Notify: Parameter Substitution: parameter "JSESSIONID4" =  "93116597F9263946ADCFD7AA3BA34555"
    Action.c(55): Notify: Parameter Substitution: parameter "lt" =  "_c68864BC7-FF33-9B55-CEC2-180473614BB9_k6BAF7C2C-AAE9-649E-1A1B-014BD23D3D94"
    Action.c(55): Found resource "https://cas:8338/cas/css/main.css" in HTML "https://cas:8338/cas/login;jsessionid=93116597F9263946ADCFD7AA3BA34555"      [MsgId: MMSG-26659]
    Action.c(55): Resource "https://cas:8338/cas/theme/red/css/userpage_spec.css" is in the cache already and will not be downloaded again      [MsgId: MMSG-26655]
    Action.c(55): Resource "https://cas:8338/cas/theme/red/css/layout.css" is in the cache already and will not be downloaded again      [MsgId: MMSG-26655]
    Action.c(55): Resource "https://cas:8338/cas/theme/red/img/logo.gif" is in the cache already and will not be downloaded again      [MsgId: MMSG-26655]
    Action.c(55): Resource "https://cas:8338/cas/js/adaliance.js" is in the cache already and will not be downloaded again      [MsgId: MMSG-26655]
    Action.c(55): Resource "https://cas:8338/cas/js/googleNewGaJsHost.js" is in the cache already and will not be downloaded again      [MsgId: MMSG-26655]
    Action.c(55): web_submit_data("login;jsessionid=D0C07C450E041F4EB71C9290570E05B3") was successful, 8205 body bytes, 627 header bytes      [MsgId: MMSG-26386]
    Action.c(71): "web_find" successful. 1 occurrence(s) of "ÄúÒÑ¾­³É¹¦µÇÂ¼ÁË£¡" found (RightOf="", LeftOf="")      [MsgId: MMSG-27196]
    Action.c(71): web_find was successful      [MsgId: MMSG-26392]
    Action.c(75): Found resource "http://ad.haowangjiao.com/css/style.css" in HTML "http://ad.haowangjiao.com/LogAccessInfo.html?fromUrl=https://cas:8338/cas/login&keyword=&sourceUrl=https://cas:8338/cas/login&uuid=E561D54B-4FBF-4915-A2A0-9E8A5DA2A8B6&linkId=-1"      [MsgId: MMSG-26659]
    Action.c(75): web_url("LogAccessInfo.html") was successful, 9435 body bytes, 414 header bytes      [MsgId: MMSG-26386]
    Ending action Action.
    Ending iteration 1.
    Ending Vuser...
    Starting action vuser_end.
    Ending action vuser_end.
    Vuser Terminated.

## 另外一种关联函数的用法：web_create_html_param_ex (“ItemValue”, “value=”, “>Ski Jacket”, “3”);

用这个函数是因为： 

Find Unpredictable Data
When Boundaries Are NOT Unique（值可能是没有边界）

这里的3表示第三个，就是左右边界条件的第三个参数（也就是满足左右条件的参数不只一个，就可以用空上函数来取值）。

建立关联的位置如：web_create_html_param("lt","name="lt" value="",""");

要放在这个参数页面之前，如之前我录制了首页然后跳转到cas，如果把这个关联放到首页之前的话就会出错，要把它放到登录页面之前（首页之后）
