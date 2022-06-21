---
layout: post
title: Selenium Xpath
categories: Selenium
tags: xpath
---

selenium大部分的方法参数都是java.lang.String locator,假如我们想传入xptah表达式,可以在表达式的开头加上"xpath=",也可以不加.如下面的两个效果是一样的.

selenium.getAttribute("//tr/input/@type")  === selenium.getAttribute("xpath=//tr/input/@type")

selenium中有一个比较特别而非常有用的方法

java.lang.Number getXpathCount(java.lang.String xpath)

通过此方法我们可以得到所有匹配xpath的数量,调用此方法,传入的表达式就不能以"xpath="

开头.

另外需要知道的是:当xpath表达式匹配到的内容有多个时,seleium默认的是取第一个,假如,我们想

自己指定第几个,可以用"xpath=(xpath表达式)[n]"来获取,例如:

selenium.getText("//table[@id='order']//td[@contains(text(),'删除')]");

在id为order的table下匹配第一个包含删除的td.

selenium.getText("xpath=(//table[@id='order']//td[@contains(text(),'删除')])[2]");

匹配第二个包含删除的td.

在调试xpath的时候,我们可以下个firefox的xpath插件,这样可以在页面上通过右键开启xpath插件.

然后随时可以检验xpath所能匹配的内容,非常方便.假如通过插件测试的xpath表达式可以匹配

到预期的内容,但是放到selenium中跑却拿不到,那么最有可能出现的问题是:在你调用seleium方法

时,传入的xpath表达式可能多加了或者是少加了"xpath=".

以下为几个常用的xpath:

    1.selenium.getAttribute("//tr/input/@type") 

    2.selenium.isElementPresent("//span[@id='submit' and @class='size:12']");

    3.selenium.isElementPresent("//tr[contains(@sytle,'display:none')]");

    4.selenium.isElementPresent("//*[contains(name(),'a')]"); //这个等价于 //a

    5.selenium.isElementPresent("//tr[contains(text(),'金钱')]");      

## xpath 深层运用

在编写Selenium案例时，少不免是要用到XPath的，现在外面关于XPath使用的参考资料很多，下面我直接转一篇关于XPath使用的文档。如果对XPath不熟悉请参考下文，你不需要去百度/Google搜索关于XPath的资料，因为下面的内容已经足够你写测试时使用，如果你已熟悉XPath，本章大可忽略跳过。

### xpath的语法

XPath 是XML的查询语言，和SQL的角色很类似。以下面XML为例，介绍XPath 的语法。

    <?xml　version="1.0"　encoding="ISO-8859-1"?>
    <catalog>
    <cd　country="USA">
    <title>Empire　Burlesque</title>
    <artist>Bob　Dylan</artist>
    <price>10.90</price>
    </cd>
    <cd　country="UK">
    <title>Hide　your　heart</title>
    <artist>Bonnie　Tyler</artist>
    <price>9.90</price>
    </cd>
    <cd　country="USA">
    <title>Greatest　Hits</title>
    <artist>Dolly　Parton</artist>
    <price>9.90</price>
    </cd></catalog>　　　　　　　　　
    </catalog>

## 定位节点

XML是树状结构，类似档案系统内数据夹的结构，XPath也类似档案系统的路径命名方式。不过XPath 是一种模式(Pattern)，可以选出 XML档案中，路径符合某个模式的所有节点出来。例如要选catalog底下的cd中所有price元素可以用：

/catalog/cd/price　　　

如果XPath的开头是一个斜线（/）代表这是绝对路径。如果开头是两个斜线（//）表示文件中所有符合模式的元素都会被选出来，即使是处于树中不同的层级也会被选出来。以下的语法会选出文件中所有叫做cd的元素（在树中的任何层级都会被选出来）：

//cd

选择未知的元素

使用星号（Wildcards,＊）可以选择未知的元素。下面这个语法会选出/catalog/cd 的所有子元素：

/catalog/cdprice

以下的语法会选出有两层父节点，叫做price的所有元素。

price

以下的语法会选择出文件中的所有元素。

    　　/div[@id='layout']div[@id='layout']/div[@class='right']/h1");
    //        System.out.println(text);
            assertEquals("登录好望角", selenium.getText("//html/body/*/div[@id='layout']/div[@class='right']/h1"));
        }
    }

## 举例

{% highlight java %}

package come.goodhope.test.aboutpage;

import javax.xml.xpath.XPath;

import com.goodhope.test.FunctionalTestCase;
import com.thoughtworks.selenium.Selenium;

import junit.framework.TestCase;

public class PageTest extends FunctionalTestCase {

    public void testpage() throws Exception{
        selenium.open("http://www.haowangjiao.com");
//        System.out.println(selenium.getTitle());
//        String XPath = "//html/head/title";
//        System.out.println(XPath);
//        System.out.println(selenium.isElementPresent(XPath));
//        String text = selenium.getText(XPath);
//        System.out.println(text);
        System.out.println(selenium.getText("//html/bodydiv[@id = "Top"]"));
    }
}

{% endhighlight %}

## xpath定位同级

如有下列页面源代码：

{% highlight html %}

<form id="aaa" name="bbb" onsubmit="return true;" action="/mmm/bbb.shtml" method="post">
<input type="hidden" name="user.id" value="1000" />
<input type="submit" id="userView_0" value="查看"/>
<input type="submit" id="userView_userEdit" name="action:userEdit" value="修改"/>
<input type="submit" id="userView_userDelete" name="action:userDelete" value="删除"/>
</form>
<form id="aaa" name="bbb" onsubmit="return true;" action="/mmm/bbb.shtml" method="post">
<input type="hidden" name="user.id" value="1001" />
<input type="submit" id="userView_0" value="查看"/>
<input type="submit" id="userView_userEdit" name="action:userEdit" value="修改"/>
<input type="submit" id="userView_userDelete" name="action:userDelete" value="删除"/>
</form>

{% endhighlight %}

这里只有<input type="hidden" name="user.id" value="1000" />可以唯一定位页面的位置，但是我们要定位的地方是它的比如说查看、修改、删除的功能，可以用xpath来定位：

xpath:"//input[@name='user.id' and @value=1000]/../input[@id='userView_0']"

