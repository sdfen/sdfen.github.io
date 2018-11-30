---
layout: post
title:  "Android WebView js交互时本地方法不被调用(部分机器)"
date:   2014-08-11
image: password.jpg
description: WebView js交互不好使了！怎么办?
category: android
tags: [android, sqlite, SqliteOpenHelper, transaction]

---

## 问题
WebView 通过js 在部分机器上无法调用本地方法

{% highlight java %}
final class JSInvokeClass{
    public void onClick(){
    }
}
{% endhighlight %}

## 解决
在本地方法上加上@JavascriptInterface

{% highlight java %}
final class JSInvokeClass{
    @JavascriptInterface
    public void onClick(){
    }
}
{% endhighlight %}
