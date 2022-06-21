---
layout: post
title: linux firefox手动升级
categories: linux
tags: firefox
---

## 1.下载Firefox-4.0 for linux

<https://www.mozilla.com/en-US/firefox/new/>

<https://www.mozilla.org/en-US/firefox/all.html>

## 2.将Firefox-4.0解压
  
tar -xjvf Firefox-latest.tar.bz2

## 3.更改解压后的文件名

解压后默认文件名为Firefox,Ubuntu 10.10中国版中, Firefox放在/usr/lib/firefox-3.6.10,为了做版本区分,将解压后的文件名更改为firefox-4.0

注：此处解压后的文件名可不更改，以后升级时，只需将firefox文件覆盖即可请注意后面的文件名需保持一致

## 4.删除旧版的firefox-3.6.10,安装新版的firefox-4.0
 
sudo rm -rf /usr/lib/firefox-3.6.10

移动新版firefox-4.0到系统目录(不移动也可以,移动是为了保持系统的目录统一)

进入新版firefox解压目录
 
sudo mv firefox-4.0 /usr/lib/

## 5.删除firefox旧的链接和建立新的链接
  
    sudo rm -rf /usr/bin/firefox
    sudo ln -s /usr/lib/firefox-4.0/firefox  /usr/bin/firefox

## 6.修改firefox系统环境目录:实现以前的菜单及面板上的图标可以正使用
  
sudo gedit /etc/apparmor.d/usr.bin.firefox

取代firefox环境目录

用firefox-4.0取代firefox-3.6.10

## 7.创建图标文件

    sudo rm -r /usr/share/pixmaps/firefox.png
    sudo cp /usr/lib/firefox-4.0/icons/mozicon128.png    /usr/share/pixmaps/firefox.png

## 实战心得：

去官方下载了个firefox8.0版，发现解压后即可使用，但为了管理方便，还许重新规划下：

    第一步：删除原来的firefox
     
    rpm -e firefox

    第二步：下载并加压压缩包，把文件夹命名为“firefox8.0”；

    第三步：把文件夹“firefox8.0”移动到“/opt” 文件夹下；

    第四步：建立命令链接（这个很重要）

    ln -s /opt/firefox8.0/firefox  /usr/bin/firefox

    第五步：创建图标文件

    cp /opt/firefox8.0/icons/mozicon128.png    /usr/share/pixmaps/firefox.png

    第六步：重启linux系统，升级完毕。