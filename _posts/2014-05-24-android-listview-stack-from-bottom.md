---
layout: post
title:  "Android ListView定位到最底"
date:   2014-05-24
image: password.jpg
description: 实现类似短信里的ListView,总显示最后一条
category: android
tags: [android, ListView, bottom]

---

类似于短信，总会显示最后一条，添加下面两属性就可以

{% highlight xml %}
android:stackFromBottom="true"
android:transcriptMode="alwaysScroll"
{% endhighlight %}

