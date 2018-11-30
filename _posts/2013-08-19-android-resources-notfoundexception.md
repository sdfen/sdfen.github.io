---
layout: post
title:  "Android 异常:Resources NotFoundException"
date:   2013-08-19
image: resources.jpg
description: setText抛出Resources$NotFoundException:String resource ID异常
category: android
tags: [android, Resources, NotfoundException, Exception, Button, TextView, setText]

---

#### 异常代码片段
{% highlight java %}
int index = 0;
textview.setText(index);
{% endhighlight %}

#### 异常信息
{% highlight java %}
//报错信息略
java.lang.RuntimeException:
Unable to start activity ComponentInfo{
  com.example.errors/com.example.errors.MainActivity//发生异常的类
}:
android.content.res.Resources$NotFoundException:String resource ID #0x0   

{% endhighlight %}


#### 异常原因

`TextView`有个`setText(int resid)`方法，但里面的int值是资源id,在R文件里有记录的，例如R.string.xx,当你随意填入一个int数，而资源文件里又没有该id时，就会报Resources$NotFoundException;

#### 异常解决
将传入的int类型转换成String 类型
{% highlight java %}

int index = 0;
textview.setText(String.valueOf(index));

{% endhighlight %}


