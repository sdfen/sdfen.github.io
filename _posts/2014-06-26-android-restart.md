---
layout: post
title:  "Android 重启应用方法"
date:   2014-06-26
image: password.jpg
description: 比较完美的重启方法
category: android
tags: [android, restart, PendingIntent]

---

比较可行的重启方法

{% highlight java %}
Intent mStartActivity = new Intent(this, SplashActivity.class); 
int mPendingIntentId = 123456; 
PendingIntent mPendingIntent = PendingIntent.getActivity( this, mPendingIntentId,mStartActivity, PendingIntent.FLAG_CANCEL_CURRENT); 
AlarmManager mgr = (AlarmManager) getApplicationContext().getSystemService(Context.ALARM_SERVICE); 
mgr.set(AlarmManager.RTC, System.currentTimeMillis() + 100, mPendingIntent); 
System.exit(0);
{% endhighlight %}
