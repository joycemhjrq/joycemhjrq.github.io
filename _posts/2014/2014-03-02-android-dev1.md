---
layout: post
title: Android开发1
categories: study
tags: android
---

## Android 开发

[Android Dev](http://developer.android.com)
Activity
Intent

	Intent intent = new Intent(this, DisplayMessageActivity.class);
	EditText editText = (EditText) findViewById(R.id.edit_message);
	String message = editText.getText().toString();
	intent.putExtra(EXTRA_MESSAGE, message);
