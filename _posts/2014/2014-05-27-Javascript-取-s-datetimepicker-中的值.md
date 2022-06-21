---
layout: post
title: javascript 取s:datetimepicker中的值
categories: javascript
tags: 
    - javascript
    - datetimepicker
---

`<s:datetimepicker/>`中的值在javascript中是无法用普通的方法读取的，struts2的官方文档给出了取值赋值的方法。

我们定义了两个s:datetimepicker，id分别是time1和time2

## Html代码

    1.<s:datetimepicker id="time1" name="time1" toggleType="explode" value="today" />   

    2.<s:datetimepicker id="time2" name="time2" toggleType="explode" value="today" />  

在javascript中可以通过以下方式读取和赋值

## Js代码 

    //取得id为time1的<s:datetimepicker/>元素  
    var time1= dojo.widget.byId("time1");  
    //用getValue()方法得到值。  
    var stringtime1 = time1.getValue();  
    //取得id为time2的<s:datetimepicker/>元素  
    var time2= dojo.widget.byId("time2");  
    //用setValue()方法赋值。  
    time2.setValue(stringtime1 ); 

# 初识Dojo widget

widget在越来越多的web应用中出现，那么，什么是widget？widget使用什么样的标记方式进行声明的呢？widget可以动态创建吗？widget有哪些常用的方法和属性呢？……这一系列的问题不由自主的浮现在脑海，下面，让我们来共同学习一下吧。

## 学习一：widget的含义

widget的英文含义是装饰物或者小器具，有些技术文档将其译为小部件，在这里直接使用其英文名，以免引起不必要的混淆。Dojo提供widget框架的原因来自两方面：一是为了更好的用户体验；二是为了帮助开发人员快速开发Web应用。


## 学习二：widget的声明

Dojo widget提供了大量可以直接使用的UI控件，它还支持标记方式的声明。其中，dojoType是声明Dojo widget的关键属性，它指明了widget的类型。如： 

    <button id="button" dojoType="Button">Create</button>.

## 学习三：在Dojo中动态创建widget

Dojo 支持动态创建widget，例如下面的代码动态创建一个Button widget，并将其添加到页面中：

javascript中取<s:datetimepicker/>中的值javascript中取<s:datetimepicker/>中的值Create Button Widget

          w=dojo.widget.createWidget("Button",{caption:"Create"});
          dojo.body().appendChild(w.domNode);

## 学习四：widget编程中常用的方法和属性

在Dojo中可以通过编程的方式改变widget的特性，下面是widget编程中常用的方法和属性。

    •dojo.widget.byId(id)：根据ID获取widget对象

    •dojo.widget.createWidget：动态创建widget对象

    •widgetObject.domNode：widget对象对应的DOM节点

    • widgetObject.containerNode：widget对象所在容器对应的DOM节点

    •widgetObject.show()/hide()：显示/隐藏widget

    •widgetObject.toggle()：切换widget的显示/隐藏状态

    •widgetObject.isShowing()：widget是否可见

# 学习五：Dojo widget的类型

目前Dojo提供了丰富的widget库，可以满足绝大多数web应用开发的需求。Dojo widget按照用途分为3大类：

    •        用于页面布局的Layout Widget
    •         用于表单增强的Form Widget
    •        实现通用功能的General Widget

 
## 总结：

Dojo widget框架具有良好的可扩展性，开发人员基于它可以开发自定义的widget，实现代码复用。