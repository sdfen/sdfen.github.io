---
layout: post
title:  "Android 画图抗锯齿"
date:   2014-05-07
image: password.jpg
description: 圆滑的边界
category: android
tags: [android, setAntialias, setDrawFilter]

---

通过Paint设置抗锯齿(效果不大理想)

{% highlight java %}
paint.setAntiAlias(true);
{% endhighlight %}

通过Canvas设置抗锯齿(效果很好)

{% highlight java %}
canvas.setDrawFilter(new PaintFlagsDrawFilter(0,Paint.ANTI_ALIAS_FLAG|Paint.FILTER_BITMAP_FLAG));
{% endhighlight %}

