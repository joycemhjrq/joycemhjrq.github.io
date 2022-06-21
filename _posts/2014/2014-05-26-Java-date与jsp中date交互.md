---
layout: post
title: java date与jsp中date交互
categories: java
tags: 
    - date
    - jsp
---

## Judge.java:

{% highlight java %}
package com.goodhope.regist;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Date;

public class Judge {

    Statement stmt;
    ResultSet rs;

    public void init() {
        try {
            Class.forName("org.postgresql.Driver");
            String url = "jdbc:postgresql://localhost:5432/postgres";
            Connection con = DriverManager.getConnection(url, "postgres",
                    "pair36");
            stmt = con.createStatement();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public boolean IfExit(String username) {
        Boolean a = false;
        init();
        String sql = "select name from cas where name='" + username + "'";
        try {
            rs = stmt.executeQuery(sql);
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            a = rs.next();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return a;
    }

    public void InsetIntoTable(String name, String pwd, Date birthday) {
        init();
        String sql = "insert into cas (name,password,birthday) values ('"
                + name + "','" + pwd + "',to_date('" + birthday
                + "','yyyy-mm-dd'))";
        try {
            stmt.execute(sql);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

{% endhighlight %}

## Regist.java

{% highlight java %}
package com.goodhope.regist;

import java.sql.Date;

import com.opensymphony.xwork2.ActionSupport;

public class Regist extends ActionSupport {

    private static final long serialVersionUID = -5254042367980236169L;

    private String username;
    private String password;
    private Date birthday;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

	public void validate() {
        if (getUsername().length() < 4 || getUsername().length() > 32)
            addFieldError("username", "用户名长度在4-32之间");//validate()这里有报错的话，页面会自//动返回input 视图（validate()数据校验方法，在执行execute()方法之前运行,但不在这个方法里面报的//addFieldError错误会根据实际情况跳转到不同的视图）
    }

    public String execute() {
        Judge j = new Judge();
        if (j.IfExit(getUsername())) {
            addFieldError("username", "用户名已经存在");
            return "error";
        } else {
            j.InsetIntoTable(getUsername(), getPassword(), getBirthday());
        }
        return "success";
    }
    public String execute() {
        Judge j = new Judge();
        if (j.IfExit(getUsername()))
            return "input";
        else {
            j.InsetIntoTable(getUsername(), getPassword(), getBirthday());
        }
        return "success";
    }
}

{% endhighlight %}

注意这里红色的部分，要区别java.sql.date 和 java.util.date

## regist.jsp

{% highlight html %}

    <%@ page contentType="text/html; charset=UTF-8"%>
    <%@ taglib uri="/struts-dojo-tags" prefix="sd"%>
    <%@ taglib uri="/struts-tags" prefix="s"%>
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
    <html>
    <head>
    <title>Regist</title>
    <s:head />
    <sd:head />
    </head>
    <body>
    <s:form action="regist" id="regist">
        <s:textfield name="username" label="用户名" id="name" onblur="test()" />
        <s:password name="password" label="密码" id="pwd" onblur="test()" />
        <sd:datetimepicker name="birthday" label="生日"
            displayFormat="yyyy-MM-dd" />
        <s:submit value="提交" onclick="return test()"  />
    </s:form>

    <SCRIPT type="text/javascript">
    function test(){
        var lname=document.getElementByIdx("name").value.length;
        if(lname<4 || lname>32){alert("用户名长度在4-32之间");
        return false;}
        var lpwd=document.getElementByIdx("pwd").value.length;
        if(lpwd<6){alert("密码长度必需大于6");return false;}
    }
    function testpassword(){  
    }
    </SCRIPT>
    </html>

{% endhighlight %}

## Judge.java:

{% highlight java %}
package com.goodhope.regist;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Date;

public class Judge {

    Statement stmt;
    ResultSet rs;

    public void init() {
        try {
            Class.forName("org.postgresql.Driver");
            String url = "jdbc:postgresql://localhost:5432/postgres";
            Connection con = DriverManager.getConnection(url, "postgres",
                    "pair36");
            stmt = con.createStatement();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public boolean IfExit(String username) {
        Boolean a = false;
        init();
        String sql = "select name from cas where name='" + username + "'";
        try {
            rs = stmt.executeQuery(sql);
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            a = rs.next();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return a;
    }

    public void InsetIntoTable(String name, String pwd, Date birthday) {
        init();
        String sql = "insert into cas (name,password,birthday) values ('"
                + name + "','" + pwd + "',to_date('" + birthday
                + "','yyyy-mm-dd'))";
        try {
            stmt.execute(sql);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

{% endhighlight %}

## Regist.java

{% highlight java %}
package com.goodhope.regist;

import java.sql.Date;

import com.opensymphony.xwork2.ActionSupport;

public class Regist extends ActionSupport {

    private static final long serialVersionUID = -5254042367980236169L;

    private String username;
    private String password;
    private Date birthday;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

	public void validate() {
	        if (getUsername().length() < 4 || getUsername().length() > 32)
	            addFieldError("username", "用户名长度在4-32之间");//这里有报错的话，页面会自动返回//input 视图（validate()数据校验方法，在执行execute()方法之前运行）
	}

    public String execute() {
        Judge j = new Judge();
        if (j.IfExit(getUsername())) {
            addActionError("用户名已经存在");
            return "error";
        } else {
            j.InsetIntoTable(getUsername(), getPassword(), getBirthday());
        }
        return "success";
    }
    public String execute() {
        Judge j = new Judge();
        if (j.IfExit(getUsername()))
            return "input";f
        else {
            j.InsetIntoTable(getUsername(), getPassword(), getBirthday());
        }
        return "success";
    }
}

{% endhighlight %}

注意这里红色的部分，要区别java.sql.date 和 java.util.date

## regist.jsp

{% highlight html %}

<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib uri="/struts-dojo-tags" prefix="sd"%>
<%@ taglib uri="/struts-tags" prefix="s"%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
<head>
<title>Regist</title>
<s:head />
<sd:head />
</head>
<body>
<s:form action="regist" id="regist">
    <s:textfield name="username" label="用户名" id="name" onblur="test()" />
    <s:password name="password" label="密码" id="pwd" onblur="test()" />
    <sd:datetimepicker name="birthday" label="生日"
        displayFormat="yyyy-MM-dd" />
    <s:submit value="提交" onclick="return test()"  />
</s:form>

<SCRIPT type="text/javascript">
function test(){
    var lname=document.getElementByIdx("name").value.length;
    if(lname<4 || lname>32){alert("用户名长度在4-32之间");
    return false;}
    var lpwd=document.getElementByIdx("pwd").value.length;
    if(lpwd<6){alert("密码长度必需大于6");return false;}
}
function testpassword(){  
}
</SCRIPT>
</body>
</html>
{% endhighlight %}

{% highlight html %}
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib uri="/struts-dojo-tags" prefix="sd"%>
<%@ taglib uri="/struts-tags" prefix="s"%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
<head>
<title>Regist</title>
<s:head />
<sd:head />
</head>
<body>
<s:form action="regist" id="regist">
    <s:textfield name="username" label="用户名" />
    <s:password name="password" label="密码" />
    <sd:datetimepicker name="birthday" label="生日"
        displayFormat="yyyy-MM-dd" />
    <s:submit value="提交" />
</s:form>
</body>
</html>

{% endhighlight %}
