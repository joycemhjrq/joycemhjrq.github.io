---
layout: post
title: junit Feed4JUnit实现数据与代码分离
categories: junit
tags: Feed4JUnit
---

[原文地址](http://www.spasvo.com/ceshi/open/kydycsgj/junit/2013412134933.html)

经常，在应用程序的业务逻辑中存在大量的这样的接口：他们接受不同的输入，然后进行或验证，或处理，进而完成相同的流程。比如网站的登录入口，用户名 和密码都有长度的限制，同时也具有是否允许特殊字符的限制等，所以在我们进行其单元测试的过程中，根据不同长度的用户名和密码，以及不同的字符组合，只需 要提供相同的测试代码结构，就能完成测试，不同的仅仅测试数据与期望值，但是因为每一个测试方法中的输入参数不同，我们必须为每一个输入组编写单独的测试 用例，从而产生大量冗余代码，十分不便于维护。

基于以上场景，JUnit 4 提供了参数化的特性，从而能够实现不同数据输入对相同测试代码的测试，如清单 1 所示：

清单 1. JUnit 4 参数化测试代码示例

{% highlight java %}

package sample.test;
import static org.junit.Assert.assertEquals;
import java.util.Arrays;
import java.util.Collection;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;
import org.junit.runners.Parameterized.Parameters;
import sample.code.UserAccess;

@RunWith(Parameterized.class)
public class JunitSample {

 private String user;
 private String pw;
 private boolean expected;

 @Parameters
 public static Collection dataGenerater() {
  return Arrays.asList(new Object[][] {
    { "user01"， "123456"， true }，
    { "helloworld"， "123456"， false }，
    { "david"， "re*ads"， false }，
    { "goodone"， "onegood"， true } });
 }

 public JunitSample(String user， String pw， boolean expected) {
  this.user = user;
  this.pw = pw;
  this.expected = expected;
 }

 @Test
 public void testAccessCheck() {
  assertEquals(expected， UserAccess.accessCheck(user， pw));
 }
}

{% endhighlight %}

　　通过以上示例代码可以看出，JUnit 4 通过使用一个标记 @Parameters 注释的返回类型为 Collection 的静态方法产生数据，测试数据通过变量传递给测试方法，从而完成多数据输入的测试。但是随着业务的需要，测试人员需要经常增加测试数据与修改现有测试数 据，JUnit 4 提供的硬编码方式已经愈显笨重和不便，数据与代码分离显得尤为重要。

　　幸好，本文所述的 Feed4JUnit 良好的解决了数据与代码分离的问题，Feed4JUnit 是 JUnit 测试框架的扩展，它通过操作来自于文件以及不同的数据源的测试数据，使您的单元测试变得更容易编写与维护。

　　本文将通过示例向您展示 Feed4JUnit 的安装以及测试代码与数据分离的实现，请注意本文的示例代码全部基于针对如下一个十分简单用户登录检验的类，并且假定您正在使用 Eclipse 作为您的 IDE，请看清单 2 类代码：

清单 2. 实例类

{% highlight java %}

package sample.code;

public class UserAccess {
 // simple validation for user name and password
 public static boolean accessCheck(String userName， String password) {
  if (userName.length() <= 4 || userName.length() > 8)
   return false;
  if (password.length() <= 4 || password.length() > 8)
   return false;
  if (userName.contains("@"))
   return false;
  if (password.contains("*"))
   return false;
  return true;
 }
}

{% endhighlight %}

## Feed4JUnit 的下载及安装

1. Feed4JUnit 是开源的测试组件，您可以从如下链接下载最新版本：

<http://sourceforge.net/projects/feed4junit/files/>

2. 解压下载的 zip 包，复制整个 lib 文件夹到您的 Java 项目的根目录，如图 1：

图 1. 复制 lib 到项目根目录

<img src="/media/img/junit-feed4junit-1.jpg">

3. 选定项目，右键选择项目的属性，然后通过 Add JARs 将步骤 2 中 lib 文件夹下的所有 Jar 添加到项目的 Build Path 下，如图 2

图 2. 添加 Jar 到 Build Path

<img src="/media/img/junit-feed4junit-2.jpg">

通过以上三步，您已经准备好您的 Feed4JUnit 环境并可以开始使用它，当然，开发测试代码之前，您必需要将 Feed4JUnit 相应的包 Import 进您的类。

使用 Feed4JUnit 实现数据与代码分离的测试

Feed4JUnit 的数据源可以包括以下几种类型 - 文件 (CSV 或者 Excel )、数据库、自定义数据源。

Feed4JUnit 使用一个特殊的运行类 Feeder.class，用来支持与标识参数化测试，如果您想要编写数据与代码分离的测试脚本，必须在您的测试类上增加注释 @RunWith(Feeder.class) 。同时，您需要使用 @Test 来标示您实现测试的方法，并且使用 @Source 来声明和接收数据源的数据，基本的代码结构如清单 3 所示：

清单 3. 基本代码结构

{% highlight java %}

package sample.test;
import static org.junit.Assert.assertEquals;
import org.databene.feed4junit.Feeder;
import org.databene.benerator.anno.Source;
import org.junit.Test;
import org.junit.runner.RunWith;

@RunWith(Feeder.class) //Specify the class will be ran as Feeder class
public class Feed4JSample {

 @Test //Specify the method as a test method
 @Source()//Specify the input data source
 public void testAccessCheck() {
  assertEquals(true， true);
 }
}

{% endhighlight %}

以文件作为数据源

Feed4JUnit 支持从 CSV 或者 Excel 文件里面读取数据作为输入，这里我们以 Excel 文件为例。

1. 在测试项目的根目录下创建 Data.xls 数据文件，样例数据如图 3，默认情况下，第一行会以列名存在，在运行过程中不会作为数据读取。

图 3. Excel 数据源

<img src="/media/img/junit-feed4junit-3.jpg">

2. 创建测试类并在接收数据的测试方法上声明数据源为 @Source("Data.xls")，Excel 中的数据在传递过程中会自动按照列与测试方法的参数的位置顺序进行匹配，并以行作为一个单位读取并传递给测试方法体。比如图 3 中的 user 列的值会做为方法的第一个参数传入方法体中，pw 列的值会作为方法的第二个参数，以此类推。在测试进行过程中，首先在 Excel 文件中读取一行(包含三列)，接着按照位置顺序将数据传递到方法体中(每列按顺序对应一个参数)进行执行，执行完成后读取 Excel 中的下一行进行相同流程的测试，其原理与 Java 中的迭代器十分类似。请注意当数据文件中数据的列数小于测试方法参数的个数的时候，测试会因为位置不匹配而失败。

清单 4. 文件数据源示例

{% highlight java %}

package sample.test;
import static org.junit.Assert.assertEquals;
import org.databene.benerator.anno.Source;
import org.databene.feed4junit.Feeder;
import org.junit.Test;
import org.junit.runner.RunWith;

import sample.code.UserAccess;

@RunWith(Feeder.class)
public class F4JfromFile {
 
 @Test
 @Source("Data.csv")//CSV source
 public void testAccessCheck_CSV(String userName， String pw， boolean expected) {
  assertEquals(expected， UserAccess.accessCheck(userName， pw));
 }
 
 @Test
 @Source("Data.xls")//Excel source
 public void testAccessCheck_Excel(String userName， String pw， boolean expected) {
  assertEquals(expected， UserAccess.accessCheck(userName， pw));
 }
}

{% endhighlight %}

3. 运行测试，因为 Feed4Junit 是 JUnit 的扩展，所以运行方式与 JUnit 完全相同，即以 JUnit 运行即可，运行结果如图 4 所示，我们可以看到，Data.xls 中的数据已全部传入测试方法并运行。

图 4. 运行结果示例

<img src="/media/img/junit-feed4junit-4.jpg">

以数据库作为数据源

通过使用 @Database ，您可以很方便的使用来自于数据库的数据，这在进行大量测试数据测试的时候或者复用现有的应用业务数据作为测试数据的情况下比较有用。

当您使用来自数据库的数据源的时候，首先必须使用 @Database 声明数据库的信息 ，可以为类或方法添加 @Database 注释 ， 如果注释类为 @Database 的时候，类中所有的方法都可以使用此数据库的数据作为源，当声明 @Database 于方法的时候，此类中仅此方法可以调用数据库作为数据源。@ Database 具有一些属性，用于声明用于连接数据库信息，请看一下说明：

id： 一个用于标识数据库数据源标识符，在测试方法的 @Source 中进行引用关联

url：数据库的 URL

driver： 数据库的驱动

user： 数据库的用户名

password：数据库的密码

完成以上数据库的定义后，需要在测试方法的 @Source 中引用您所需要的库，使用属性 id 和 selector 可以完成此操作：

id： @Source 中的 id 和 @Database 的 id 相对应关联

selector：SQL 语句，用于查询出相应的数据传递给测试方法

以下我们以 DB2 作为数据源，使用 DB2 的 Sample 数据库，并创建名为 TEST 的表来存储测试数据，测试数据与图 3 Excel 数据源的完全相同，请看图 5。

图 5. 数据表中的测试数据

<img src="/media/img/junit-feed4junit-5.jpg">

首先，创建测试类，添加 @Database 注释并增加数据库的连接信息，同时指定一个表示数据库的 id，在测试方法的 @Source 中通过 id 进行关联，并制定 selector 的语句进行数据查询，例如本例中的 selector = "select * from TEST"，会从 TEST 表中取出全部数据用于测试，细节请参考以下代码示例：

清单 5 为在类上声明 @Database。

{% highlight java %}

package sample.test;
import static org.junit.Assert.assertEquals;
import org.databene.benerator.anno.Database;
import org.databene.benerator.anno.Source;
import org.databene.feed4junit.Feeder;
import org.junit.Test;
import org.junit.runner.RunWith;
import sample.code.UserAccess;

@RunWith(Feeder.class)
@Database(
  id = "testdb"，
  url = "jdbc:db2://localhost:50000/SAMPLE"，
  driver = "com.ibm.db2.jcc.DB2Driver"，
  user = "db2admin"，
  password = "db2admin")
public class F4JfromDB {

 @Test
 @Source(id = "testdb"， selector = "select * from TEST")
 public void testAccessCheck(String userName， String pw， String expected) {
  Boolean bSucess = UserAccess.accessCheck(userName.trim()， pw.trim());
  assertEquals(expected.trim()， bSucess.toString());

 }
}

{% endhighlight %}

清单 6. 在方法上声明 @Database

{% highlight java %}

@RunWith(Feeder.class)
public class F4JfromDB_Method {
 @Test
 @Database(
   id = "testdb"，
   url = "jdbc:db2://localhost:50000/SAMPLE"，
   driver = "com.ibm.db2.jcc.DB2Driver"，
   user = "db2admin"，
   password = "db2admin")
 @Source(id = "testdb"， selector = "select * from TEST")
 public void testAccessCheck(String userName， String pw， String expected) {
  Boolean bSucess = UserAccess.accessCheck(userName.trim()， pw.trim());
  assertEquals(expected.trim()， bSucess.toString());

 }
}

{% endhighlight %}

测试运行过程中，通过 url，driver 等信息建立数据连接，通过 selector 发出数据请求，最后完成查询并把数据传递给测试方法，数据在传递给方法的时候，会按数据表的列的顺序与参数进行匹配，运行结果与图 4 类似。

自定义数据源

除了 CSV，Excel 和数据库的数据源外，Feed4JUnit 还提供自定义数据源，以满足不同用户的需求，用户同样可以通过封装 JUnit 4 提供的参数化测试的方法来完成数据源自定义，所有这里作者不再详述，用户可以封装并取得不同的数据源的数据，传递给 Feed4JUnit 的相应接口，来完成数据源的自定义。

结束语

本文通过对比介绍和简单易懂的实例全面讲解了 Feed4JUnit 对数据与代码分离的测试支持。通过提供简单的注释，Feed4JUnit 使用户能够极其方便的实施数据与代码分离的测试，极大地增强了 JUnit 测试框架的易用性。 相信您已经在本文的叙述中看到它的优点。同时，本文所叙述的仅仅是 Feed4JUnit 提供的测试增强功能的一部分，Feed4JUnit 同时还提供了大量数据的随机测试和等价类测试等众多功能，如果您感兴趣可以自行参考。
