---
layout: post
title:  "Android LinearLayout 设置分割线divider"
date:   2014-05-25
image: password.jpg
description: 简单设置linearLayout分割线
category: android
tags: [android, LinearLayout, divider]

---

## 调用系统分割线
一般只需要在xml文件里设置showDividers 和 divider就可以，以下是调用系统的分割线

{% highlight xml %}
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:showDividers="middle"
    android:divider="?android:attr/listDivider" />
{% endhighlight %}
要是在代码里设置

{% highlight java %}
setShowDividers 
setShowDivider
{% endhighlight %}

## 自定义分割线
注意，如果直接给divider颜色值是不会生效的，需要用shape定义

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<shape
    xmlns:android="http://schemas.android.com/apk/res/android"> 
    <size  
        android:width="200"
        android:height="1dp" />
    <solid  
        android:color="#fff" />
</shape>
{% endhighlight %}

