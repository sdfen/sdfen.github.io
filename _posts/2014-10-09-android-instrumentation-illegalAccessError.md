---
layout: post
title:  "Android 异常：Instrumentation run failed due to java.lang.IllegalAccessError"
date:   2014-10-09
image: password.jpg
description: 用Intellij试调的看过来了…郁闷的问题
category: android
tags: [android, Instrumentation, IllegalAccessError, library]

---

## 问题
运行报错
`Test failed to run to completion. Reason: 'Instrumentation run failed due to 'java.lang.IllegalAccessError''. Check device logcat for details
Test running failed: Instrumentation run failed due to 'java.lang.IllegalAccessError'`

## 解决
这是因为library重复导入引致的，导入library项目时把Compile换成Provided就解决了

<img src="{{ '/assets/img/posts/illegalaccess.png' | prepend: site.baseurl }}" alt=""> 

方法[来源](http://stackoverflow.com/questions/14839162/instrumentation-run-failed-due-to-java-lang-illegalaccesserror)
