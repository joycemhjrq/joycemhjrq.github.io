---
layout: post
title: PHP远程获取文件乱码问题
categories: study
tags: php
---

## 2014-3-3
===========

* PHP 

php `file_get_contents`读取远程文件的乱码问题(gzip压缩引起的)

`curl` 解决

	function curl_get($url, $gzip=false){
	    $curl = curl_init($url);
	    curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
	    curl_setopt($curl, CURLOPT_CONNECTTIMEOUT, 10);
	    if($gzip) curl_setopt($curl, CURLOPT_ENCODING, "gzip"); //关键在这里
	    $content = curl_exec($curl);
	    curl_close($curl);
	    return $content;
	}

`file_get_contents` 解决
	
	$url = 'http://m.weather.com.cn/data/101170101.html';
	echo '<pre>'; 
	//打开gzip压缩过的页面, 路径前不加compress.zlib:// 打开会有乱码。
	print_r(file_get_contents("compress.zlib://".$url)); 　

composer
xampp
goutte.phar