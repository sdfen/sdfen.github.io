---
layout: post
title:  "Java 经验法则5 避免创建不必要的对象"
date:   2013-12-13
image: password.jpg
description: 不要浪费宝贵的系统资源
categories: [android, java]
tags: [android, java, sigleton, constructor, 私有构造方法]

---

一般来说，最好能重用对象而不是在每次需要的时候就创建一个相同功能的新对象。重用方式既快速，又流行。

举个极端反面栗子：
{% highlight java %}
//请不要这样做
String s = new String("abc");
{% endhighlight %}

该语句每次执行的时候都创建一个新的String实例，但是这些创建对象的动作全都是不必要的，传递给String构造方法的参数”abc”本身就是一个String实例。如果这种用法是在一个循环中，或者是在一个被频繁调用的方法中，就会创建出成千上万不必要的String实例。

正确地姿势：
{% highlight java %}
String s = "abc";
{% endhighlight %}

这种方法并不是每次执行都创建新的实例，而且，它可以保证，对于所有在同一虚拟机中运行的代码，只要它们包含相同的字符串常量，该对象就会被重用；

对于同时提供静态工厂方法和构造方法的不可变类，通常使用静态工厂方法而不是构造器，以避免创建不必要的对象。例如，静态工厂方法Boolean.valueOf(String)几乎总是优先于构造方法Boolean(String)。构造方法在每次被调用的时候都会创建一个新的对象，而静态工厂方法则从来不要求这样。

基本类型运算时，避免和装箱基本类型混用，例如

{% highlight java %}
Long sum = 0L;
for(long i=0; i<Integer.MAX_VALUE; i++){
    sum += i
}
{% endhighlight %}

由于打错了字符将long 写成 Long,会倒置运算很慢！！



