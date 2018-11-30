---
layout: post
title:  "Android 异常：PowerManager.WakeLock under-locked"
date:   2014-07-15
image: password.jpg
description: 有用PowerManager.WakeLock来实现屏幕常亮的同学可以看看
category: android
tags: [android, PowerManager, WakeLock,under-locked,exception]

---

## 问题
在使用`PowerManager.WakeLock的release()`方法是有时会报java.lang.RuntimeException: WakeLock under-locked的异常

## 解决

先调用`setReferenceCounted(false);`方法再调用`release()`
