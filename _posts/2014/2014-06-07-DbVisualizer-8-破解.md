---
layout: post
title: DbVisualizer 8.0.1破解
categories: DbVisualizer
tags: DbVisualizer
---

DbVisualizer 8.01 未破解下载地址  <www.xiaolinzi.com/soft/4405.html>

[破解文件下载](http://www.r-base.net/wp-content/uploads/blog/DbVisualizer8.0.1-crack.zip)

## 一、破解方法:

1. 把lib/dbvis.jar里面的这个文件dbvis.puk替换掉(用WinRAR打开dbvis.jar即可替换)

2. Help->License Key 导入dbvis.license(也可以把dbvis.license放在dbvis的根目录里,这种方式在zip版本试过)

## 二、修改Dbvis的默认快捷键

 dbvis的SQL提示(SQL补充)默认的快捷键是 Ctrl + Space (即是我们切换输入法的快捷键),这是一个国外工具的一个通病(很多工具的默认代码提示也正好是我们切换输入法的快捷键,经常会有冲突). 这样Dbvis用起来会很不爽,要经常手工改快捷键.
  后来我研究了一下dbvis的jar包,让我找到了一种修改默认快捷键的方式.

修改SQL提示的步骤如下:

1, 编辑dbvis.jar包下的dbvis-actions.xml文件(解压或直接修改)

2, 找到以下的代码

	[xml] view plaincopy
	<span style="color:#009900"><strong><action</strong> <span style="color:#000066">idref</span>=<span style="color:#ff0000">"show-auto-completion-command"</span> <span style="color:#000066">keystroke</span>=<span style="color:#ff0000">"control SPACE"</span><strong>/></strong></span>   

3, 将以上代码修改为以下代码 (改成 alt + 点 或 alt + 斜杠 )

	[xml] view plaincopy
	<span style="color:#009900"><strong><action</strong> <span style="color:#000066">idref</span>=<span style="color:#ff0000">"show-auto-completion-command"</span> <span style="color:#000066">keystroke</span>=<span style="color:#ff0000">"alt PERIOD:alt SLASH"</span><strong>/></strong></span>   

4, 保存文件并放回到dbivs.jar中.

经过以上步骤便让dbvis的SQL提示的快捷键与eclipse的代码提示快捷键一致,修改在与我们编写代码习惯一致,提高我们的编写SQL的效率.

PS: 我觉得Dbvis最大的好处是我可以用相同的方法来管理不同的数据库,让我在操作与管理不同的数据库时,不需要去学习相对应的数据库管理工具.

