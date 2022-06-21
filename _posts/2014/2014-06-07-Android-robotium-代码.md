---
layout: post
title: Android robotium 代码
categories: Android
tags: robotium
---

## robotium 代码

{% highlight java %}

ArrayList<EditText> EditTexts =   solo.getCurrentEditTexts();
solo.enterText(EditTexts.get(0), "");
solo.enterText(EditTexts.get(0), "username");
Assert.assertEquals("Doubanshuo", solo.getCurrentActivity().getTitle());
solo.enterText(0, "\n");
solo.enterText(EditTexts.get(1), "");
Assert.assertEquals("Password", EditTexts.get(1).getHint());
solo.enterText(EditTexts.get(1), "password");
solo.clickOnButton("Login");
solo.waitForActivity("doubanshuo");
solo.assertCurrentActivity("shibushihome", Home.class);
Home hact = (Home) solo.getCurrentActivity();
View iv = hact.findViewById(com.douban.shuo.R.id.button4);
solo.clickOnView(iv);
solo.waitForActivity("setting");
solo.assertCurrentActivity("shibushihome", Setting.class);

TextView quit = (TextView) solo.getCurrentActivity().findViewById(com.douban.shuo.R.id.text4);
solo.clickOnView(quit);

solo.waitForDialogToClose((long) 100);
solo.clickOnButton(0);

{% endhighlight %}
