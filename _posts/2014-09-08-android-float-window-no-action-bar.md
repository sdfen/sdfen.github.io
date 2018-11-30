---
layout: post
title:  "Android 无状态栏全屏悬浮窗"
date:   2014-09-08
image: password.jpg
description: 浮窗无法显示在通知栏上？赶紧进来看看吧
category: android
tags: [android, float, window, WindowManager]

---

通过以下的方法可以实现一个无状态栏全屏的浮层


{% highlight java %}
WindowManager.LayoutParams lp = new WindowManager.LayoutParams();
lp.type = WindowManager.LayoutParams.TYPE_SYSTEM_ERROR;
lp.flags = WindowManager.LayoutParams.FLAG_FULLSCREEN | WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN;
{% endhighlight %}

