---
layout: post
title:  "Java 经验法则10 始终要复写toString"
date:   2014-01-12
image: password.jpg
description: 经常被忽略的一个方法
categories: [android, java]
tags: [android, java, toString]

---

虽然遵守toString约定并不像遵守equals和hashCode的约定那么重要，但是提供好的toString实现可以使类用起来更加舒适。默认的toString实现会返回一串类似“Person@31g234n”的字符串，这对于开发调试确实不太友好，所以需要复写它。

在实际应用中，toString方法应该返回对象中包含的所有值得关注的信息。例如在Person类里，年龄、身高、性别等。