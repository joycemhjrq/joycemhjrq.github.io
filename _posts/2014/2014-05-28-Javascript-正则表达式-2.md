---
layout: post
title: javascript 正则表达式2
categories: javascript
tags: regex
---

我们可以使用JavaScript编写以下脚本，验证用户输入的邮件地址的有效性。 

    <pre name="code" class="java"><html>   
    <head>   
    　 <script language="Javascript1.2">   
    　　　　 <!-- start hiding   
    　　　　 function verifyAddress(obj)   
    　　　　　{   
    　　　　　　var email = obj.email.value;   
    　　　　　　var pattern = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/;   
    　　　　　　flag = pattern.test(email);   
    　　　　　　if(flag)   
    　　　　　　{   
    　　　　　　　alert(“Your email address is correct!”);   
    　　　　　　　return true;   
    　　　　　　}   
    　　　　　　else   
    　　　　　　　{   
    　　　　　　　　alert(“Please try again!”);   
    　　　　　　　　return false;   
    　　　　　　　 }   
    　　　　　 }   
    　　　　 // stop hiding -->   
    　　　 </script>   
    　　</head>   
    　 <body>   
    　　 <form onSubmit="return verifyAddress(this);">   
    　　　 <input name="email" type="text">   
    　　　 <input type="submit">   
    　　　 </form>   
    　　</body>   
    </html>   
    </pre>  
 
<span style="font-size: 18pt;">正则表达式对象</span>  

本对象包含正则表达式模式以及表明如何应用模式的标志。 

    <pre name="code" class="java">

        语法 1 re = /pattern/[flags]  
        语法 2 re = new RegExp("pattern",["flags"])  

    </pre>  

参数  re  必选项。将要赋值为正则表达式模式的变量名。   
  
Pattern  必选项。要使用的正则表达式模式。如果使用语法 1，用 "/" 字符分隔模式。如果用语法 2，用引号将模式引起来。   
  
Flags  可选项。如果使用语法 2 要用引号将 flag 引起来。标志可以组合使用，可用的有： 

    <pre name="code" class="java">g （全文查找出现的所有 pattern）   
        i （忽略大小写）   
        m （多行查找）   
    </pre>  
  
<span style="font-size: 18pt;">示例</span>  

下面的示例创建一个包含正则表达式模式及相关标志的对象(re)，向您演示正则表达式对象的用法。在本例中，作为结果的正则表达式对象又用于 match 方法中：

    <pre name="code" class="java">function MatchDemo()  
    {  
        var r, re; // 声明变量。  
        var s = "The rain in Spain falls mainly in the plain";  
        re = new RegExp("ain","g"); // 创建正则表达式对象。  
        r = s.match(re); // 在字符串 s 中查找匹配。  
        return(r);   
    }  
    </pre>  
  
返回值： ain,ain,ain,ain\\  

属性 lastIndex 属性 | source 属性\\  

方法 compile 方法 | exec 方法 | test 方法\\  

要求 版本 3\\  

请参阅 RegExp 对象 | 正则表达式语法 | String 对象\\  
  
<span style="font-size: 18pt;">exec 方法</span>  

用正则表达式模式在字符串中运行查找，并返回包含该查找结果的一个数组。 

rgExp.exec(str)  
  
参数  rgExp 必选项。包含正则表达式模式和可用标志的正则表达式对象。   
  
str 必选项。要在其中执行查找的 String 对象或字符串文字。   
  
说明\\  

如果 exec 方法没有找到匹配，则它返回 null。如果它找到匹配，则 exec 方法返回一个数组，并且更新全局 RegExp 对象的属性，以反映匹配结果。数组的0元素包含了完整的匹配，而第1到n元素中包含的是匹配中出现的任意一个子匹配。这相当于没有设置全局标志 (g) 的 match 方法。 

如果为正则表达式设置了全局标志，exec 从以 lastIndex 的值指示的位置开始查找。如果没有设置全局标志，exec 忽略 lastIndex 的值，从字符串的起始位置开始搜索。  
exec 方法返回的数组有三个属性，分别是 input、index 和 lastIndex。Input 属性包含了整个被查找的字符串。Index 属性中包含了整个被查找字符串中被匹配的子字符串的位置。LastIndex 属性中包含了匹配中最后一个字符的下一个位置。  
  
示例\\ 

下面的例子举例说明了 exec 方法的用法： 

    <pre name="code" class="java">function RegExpTest()  
    {  
        var ver = Number(ScriptEngineMajorVersion() + "." + ScriptEngineMinorVersion())  
        if (ver >= 5.5){ // 测试 JScript 的版本。  
            var src = "The rain in Spain falls mainly in the plain.";  
            var re = /\w+/g; // 创建正则表达式模式。  
            var arr;  
            while ((arr = re.exec(src)) != null)  
            document.write(arr.index + "-" + arr.lastIndex + arr + "\t");  
        }  
        else{  
            alert("请使用 JScript 的更新版本");  
        }  
    }  
    </pre>  
  
返回值：0-3The 4-8rain 9-11in 12-17Spain 18-23falls 24-30mainly 31-33in 34-37the 38-43plain  
  
test 方法\\  

返回一个 Boolean 值，它指出在被查找的字符串中是否存在模式。  

rgexp.test(str)   
  
参数\\  

rgexp  必选项。包含正则表达式模式或可用标志的正则表达式对象。   
  
str  必选项。要在其上测试查找的字符串。   
  
说明  

test 方法检查在字符串中是否存在一个模式，如果存在则返回 true，否则就返回 false。  

全局 RegExp 对象的属性不由 test 方法来修改。  
  
示例  

下面的例子举例说明了 test 方法的用法：  

    <pre name="code" class="java">function TestDemo(re, s)  
    {  
        var s1; // 声明变量。  
        // 检查字符串是否存在正则表达式。  
        if (re.test(s)) // 测试是否存在。  
        s1 = " contains "; // s 包含模式。  
        else  
        s1 = " does not contain "; // s 不包含模式。  
        return("'" + s + "'" + s1 + "'"+ re.source + "'"); // 返回字符串。  
    }  
    </pre>  
  
函数调用：document.write (TestDemo(/ain+/ ,"The rain in Spain falls mainly in the plain."));  
  
返回值：'The rain in Spain falls mainly in the plain.' contains 'ain+'  
  
<span style="font-size: 18pt;">match 方法</span>  

使用正则表达式模式对字符串执行查找，并将包含查找的结果作为数组返回。\\  

stringObj.match(rgExp)   
  
参数\\  

stringObj   

必选项。对其进行查找的 String 对象或字符串文字。   
  
rgExp  

必选项。为包含正则表达式模式和可用标志的正则表达式对象。也可以是包含正则表达式模式和可用标志的变量名或字符串文字。   
  
说明\\ 

如果 match 方法没有找到匹配，返回 null。如果找到匹配返回一个数组并且更新全局 RegExp 对象的属性以反映匹配结果。  

match 方法返回的数组有三个属性：input、index 和 lastIndex。Input 属性包含整个的被查找字符串。Index属性包含了在整个被查找字符串中匹配的子字符串的位置。LastIndex 属性包含了最后一次匹配中最后一个字符的下一个位置。  

如果没有设置全局标志 (g)，数组的 0 元素包含整个匹配，而第 1 到 n 元素包含了匹配中曾出现过的任一个子匹配。这相当于没有设置全局标志的 exec 方法。如果设置了全局标志，元素 0 到 n 中包含所有匹配。  