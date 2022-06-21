---
layout: post
title: jmeter BSF BeanShell
categories: jmeter
tags: 
    - jmeter
    - BSF
    - BeanShell
---

## BSF

jmeter无法自行处理javascript，但是它可以用自带的BSF PreProcessor(BSF:面向java的脚本语言，支持javascript)

(使用这个之前要把bsh-2.0b2.jar包放到jmeter的lib下)

以出售带号寄售信息为例：

页面里有一个totalCommission的变量，它的值是根据price和amount的值来计算的，这里可以在发布带号寄售的取样器请求页面添加一个前置处理器：BSF PreProcessor

如下图：

<img src="/media/img/jmeter-bsf-1.jpg">

其中的script脚本为：

	vars.put('price',${price});
	var priceInput =vars.get('price');

（这里要使用变量的时候都要声明，如这里：vars.put('price',${price});你直接用log.info(vars.get(price))的话会报错，要先声明它：var priceInput =vars.get('price');然后再log.info(priceInput)的话就OK了，也不知道why：世界之大，无奇不有！！）

	vars.put('amount',${amount})
	var quantity =vars.get('amount');
	var commissionRate=vars.get('commissionRate');
	var totalCommission=vars.get('totalCommission');（注意这里要定义var totalCommission,如果直接用vars.get('totalCommission')会报错，至于why还不是太清楚）
	log.info("before total:"+priceInput);
	log.info('commissionRate='+commissionRate);

	var additionalCommission=vars.get("additionalCommission");

	log.info('additionalCommission='+additionalCommission);

	var commissiontext;
	commissiontext = priceInput*commissionRate;
	commissiontext = commissiontext.toFixed(3);

	log.info('commissiontext='+commissiontext);

	var temp = parseInt(commissiontext);

	log.info('temp='+temp);

	if(temp<commissiontext)
	{temp+=1;}
	commissiontext=parseInt(temp) + parseInt(additionalCommission);
	log.info('commissiontext1='+commissiontext);

	vars.put("totalCommission",commissiontext);

	log.info("after total:"+vars.get('totalCommission'));

<img src="/media/img/jmeter-bsf-2.jpg">

处理中出现问题的话，可以到jmeter的bin下面查看jmeter.log看看问题出在哪，相应的就可以解决它了

## BeanShell

上述问题也可以用BeanShell PreProcessor来解决：

BeanShell是一个小型的，免费的，可嵌入式的，具有面向对象脚本语言特性的Java代码解释器。它是用Java语言写的。它能执行标准的Java语句和表达式，还自带简单的脚本命令和语法。它把编程对象当成一个简单的方法，这很像Perl和JavaScript. 你可以在写Java测试或调试时使用BeanShell，也可以用它作为你的应用程序的脚本引挚。简而言之，BeanShell可以动态的解释JAVA语言。也就是说BeanShell在许多方面对于Java的用处就像Tcl/Tk对于C的用处一样：BeanShell是可嵌入式的---你可以在运行时从你的应用程序调用BeanShell去动态的执行Java代码或是为你的应用程序提供脚本扩展。相反，你也可以从BeanShell调用你的应用程序及其对象，它可以让JAVA对象和API动态运行。正因为BeanShell是用JAVA写的，所以它可以和你的应用程序运行在同一个JVM空间内，你也可以自由的传递实时对象的参照(References)到脚本代码中并且作为结果返回。

这里跟BSF一样新建一个前置处理器：BeanShell PreProcessor

代码如下：

	var priceInput=${price};
	log.info("price="+priceInput);
	var quantity=${amount};
	log.info("quantity="+quantity);

	var commissionRate=vars.get("commissionRate");
	float abc=Float.parseFloat(commissionRate);
	log.info("commissionRate="+abc);

	float commissiontext=priceInput*abc;
	log.info("commissiontext="+commissiontext);

	int temp=(int)commissiontext+1;
	log.info("temp="+temp);

	var additionalCommission=vars.get("additionalCommission");
	log.info("additionalCommission="+additionalCommission);
	commissiontext=temp +Integer.parseInt(additionalCommission);
	log.info("="+commissiontext);

	vars.put("totalCommission",String.valueOf(commissiontext));

因为BeanShell的变量定义比较宽松，所以很容易在转换的时候出错，这里要很注意。

.BeanShell 脚本处理：如果在测试用例中用到了 BeanShell 脚本，则需要将 BeanShell 包拷贝到 JMeter bin 目录下。BeanShell 是一种兼容 Java 语言的轻量级脚本语言，JMeter 脚本中可能会经常用它来做日志处理，正则表达式后处理（Post- Process）等。如果在测试用例中用到了 Mail Visualiser, Mail Reader 以及 Web Services (SOAP) sampler，则需要将 MAIL 包拷贝到 JMeter bin 目录下。如果在测试用例中用到了 JMS 相关的 sampler，则需要将 JMS 包拷贝到 JMeter bin 目录下。

http://www.ibm.com/developerworks/cn/webservices/1004_sunch_jmeter_soap/index.html
