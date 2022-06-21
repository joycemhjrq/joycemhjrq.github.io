---
layout: post
title: Windows win7 bios
categories: Windows
tags: bios
---

win7进入bios：打开电脑时电脑屏幕下方有提示，本机为例，提示为：F12->boot system，打开电脑时点击F12进入后选择：【Enter Setup】然后左右键选择你需要的功能。
win7安装windows xp mode（xp虚拟机），先到http://www.grc.com/files/securable.exe下载它，然后运行检测你的电脑是否可以安装虚拟机。如下图：

<img src="/media/img/win7-bios.jpg">

如果第二位显示：yes就表示支持，如果显示no就表示不支持，如果显示Off就表示支持，但是被bios禁用，可以点击About查看解决办法（这里的解决办法是进入bios，找到Execution Disable或No Execute Bit或者相似的选项，把它置为：Aviable）。

第三位是：Locked On表示bios支持该功能，如果为Locked Off就表示不支持，要手动设置打开：进入bios后找到Virtualization Technology把它置为Aviable状态就可以了，更改完后重启电脑，再用这个软件检测一下，看是不是上图那样的显示，如果是就可以安装虚拟机了。