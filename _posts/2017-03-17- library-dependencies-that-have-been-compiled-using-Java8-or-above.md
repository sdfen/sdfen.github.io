---
layout: post
title:  "Android Studio Error:library dependencies that have been compiled using Java 8 or above"
date:   2017-03-17
image: fruits.jpg
description: java 8项目导入java 7项目编译报错
category: android
tags: [android, java8, java7, jdk]
---

## 问题
Android studio 编译项目时报以下错误

{% highlight java %}
Error:Error converting bytecode to dex:
Cause: Dex cannot parse version 52 byte code.
This is caused by library dependencies that have been compiled using Java 8 or above.
If you are using the 'java' gradle plugin in a library submodule add 
targetCompatibility = '1.7'
sourceCompatibility = '1.7'
to that submodule's build.gradle file.
Error:1 error; aborting
:app:transformClassesWithDexForDebug FAILED
{% endhighlight %}

## 原因
经过各种排查，发现是因为导入了一个类库导致
`compile 'com.google.guava:guava:21.0'`
应该是由于该库是基于java7编写的，而我的项目是基于java8，导致了冲突

## 解决
不用该库，或降低java版，目前google还没有好的解决方法