---
layout: post
title: DbVisualizer 常见问题
categories: DbVisualizer
tags: DbVisualizer
---

## DbVisualizer 8 解决中文乱码问题

在SQL Commander中，sql语句中如果有中文，显示是‘口口口’。

解决办法如下：

在Tools->tool Properties->General->Appearance->Fonts->SQL Editor选择新宋体（14），同时把Grid也选择新宋体（或者其它可以显示中文的字体）就可以了。

如上配置以后，中文就可以正常显示了。
