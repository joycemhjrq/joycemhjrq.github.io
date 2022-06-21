---
layout: post
title: jmeter Counter
categories: jmeter
tags: 
    - jmeter
    - Counter
---

## jmeter Counter

<img src="/media/img/jmeter-count-1.jpg">

如果要得到这个Counter的值，可以这样使用：${counter}:这里的counter是引用名称
下面是jmeter官网的解释：

Control Panel

<img src="/media/img/jmeter-count-2.jpg">

Parameters

<table cellspacing="0" cellpadding="2" border="0">
<tbody>
<tr>
<td>
<p>
<b>Control Panel</b>
</p>
<div align="center">
<img width="399" height="244" title="jmeter <wbr>Counter" alt="jmeter <wbr>Counter" name="image_operate_88681312791020426" real_src="http://jakarta.apache.org/jmeter/images/screenshots/counter.png" src="http://jakarta.apache.org/jmeter/images/screenshots/counter.png" action-data="http%3A%2F%2Fjakarta.apache.org%2Fjmeter%2Fimages%2Fscreenshots%2Fcounter.png" action-type="show-slide">
</div>
<p>
<b>Parameters</b>
</p>
<table cellspacing="0" cellpadding="2" border="1">
<tbody>
<tr>
<th>Attribute</th>
<th>Description</th>
<th>Required</th>
</tr>
<tr>
<td>Name</td>
<td>Descriptive name for this element that is shown in the tree.</td>
<td>No</td>
</tr>
<tr>
<td>Start</td>
<td>The starting number for the counter. The counter will equal this number during the first iteration.</td>
<td>Yes</td>
</tr>
<tr>
<td>Increment</td>
<td>How much to increment the counter by after each iteration.</td>
<td>Yes</td>
</tr>
<tr>
<td>Maximum</td>
<td>If the counter exceeds the maximum, then it is reset to the Start value. For versions after 2.2 the default is Long.MAX_VALUE (previously it was 0).</td>
<td>No</td>
</tr>
<tr>
<td>Format</td>
<td>Optional format, e.g. 000 will format as 001, 002 etc. This is passed to DecimalFormat, so any valid formats can be used. If there is a problem interpreting the format, then it is ignored. [The default format is generated using Long.toString()]</td>
<td>No</td>
</tr>
<tr>
<td>Reference Name</td>
<td>
This controls how y
ou refer to this value in other elements. Syntax is as in
<a href="http://jakarta.apache.org/jmeter/usermanual/functions.html">user-defined values</a>
:
<code>$(reference_name}</code>
.
</td>
<td>Yes</td>
</tr>
<tr>
<td>Track Counter Independently for each User</td>
<td>In other words, is this a global counter, or does each user get their own counter? If unchecked, the counter is global (ie, user #1 will get value "1", and user #2 will get value "2" on the first iteration). If checked, each user has an independent counter.</td>
<td>No</td>
</tr>
</tbody>
</table>
</td>
</tr>
<tr>
<td>
<br>
</td>
</tr>
</tbody>
</table>

