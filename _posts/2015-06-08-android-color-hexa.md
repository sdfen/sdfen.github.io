---
layout: post
title:  "Android Color(int)值与hexa(字符串)相互转换"
date:   2015-06-08
image: password.jpg
description: olor 值转 hexa 字符串 hexa 字符串转 color 值
category: android
tags: [android, color, hexa]

---


{% highlight java %}
//color 值转 hexa 字符串
String strColor = String.format("#%06X", 0xFFFFFF & intColor);
//hexa 字符串转 color 值
int color = Color.parseColor("#87CEFA");
{% endhighlight %}
