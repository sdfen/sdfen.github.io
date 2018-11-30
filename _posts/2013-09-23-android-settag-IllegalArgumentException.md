---
layout: post
title:  "Android setTag抛出IllegalArgumentException异常"
date:   2013-09-23
image: password.jpg
description: java.lang.IllegalArgumentException:The key must be an application-specific resource id
category: android
tags: [android, setTag, IllegalArgumentException, Exception]

---


#### 问题

调用`setTag(int key, Object tag)`时报错：`java.lang.IllegalArgumentException: The key must be an application-specific resource id.`

#### 原因
官方的api文档中提到：`The specified key should be an id declared in the resources of the application to ensure it is unique (see the ID resource type). Keys identified as belonging to the Android framework or not associated with any package will cause an IllegalArgumentExceptionto be thrown.`所以抛出IllegalArgumentException的原因就在于key不唯一，那么如何保证这种唯一性呢？定义一个final类型的int变量和硬编码一个值的方式都是行不通的。

#### 解决
先在 res/values/strings.xml中添加
{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <item type="id" name="tag_first"></item>
  <item type="id" name="tag_second"></item>    
</resources>
{% endhighlight %}

代码调用setTag(int key,Object tag)
{% highlight java %}
view.setTag(R.id.tag_first,"Hello");
view.setTag(R.id.tag_second,"Success");
{% endhighlight %}


 
#### 完...





