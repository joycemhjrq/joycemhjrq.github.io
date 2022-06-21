---
layout: post
title: javascript 正则表达式 实践
categories: javascript
tags: regex
---

## 校验是否全由数字组成

function isDigit(s)
{
var patrn=/^[0-9]{1,20}$/;
if (!patrn.exec(s)) return false
return true
}

//校验登录名：只能输入5-20个以字母开头、可带数字、“_”、“.”的字串