---
layout: post
title:  "Android 2.3以下Json解析错误(bom)"
date:   2013-09-23
image: password.jpg
description: 2.3及以下的系统解析json时报错:JSONException:Value of type java.lang.String cannot be converted to JSONObject
category: android
tags: [android, json, JSONException, bom, android2.3]

---


#### 问题

2.3及以下的系统版本在解析json数据时发现报 10-12 02:36:35.964: W/System.err(323): `org.json.JSONException: Value of type java.lang.String cannot be converted to JSONObject` 错误。

#### 原因
当UTF-8编码带有bom头时，json无法解析

#### 解决

##### 方法一
服务器返回json时，编码utf-8不要带bom

##### 方法二
对比android4.0的json包

4.0 代码
{% highlight java %}
public JSONTokener(String in) {
  // consume an optional byte order mark (BOM) if it exists
  if (in != null && in.startsWith("ufeff")) {
    in = in.substring(1);
  }
  this.in = in;
}
{% endhighlight %}

而2.3的代码
{% highlight java %}
public JSONTokener(String in) {
  is.in = in;
}
{% endhighlight %}

问题很简单了在解析前将String过滤一下就ok。


 
#### 完...





