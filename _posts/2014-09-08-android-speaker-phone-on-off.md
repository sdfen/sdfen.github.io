---
layout: post
title:  "Android 开启/关闭免提功能实现"
date:   2014-09-08
image: password.jpg
description: 短短几行代码就能实现开启关闭免提功能，你还等什么。
category: android
tags: [android, phoneOn, AudioManager, MODE_IN_CALL]

---

## 添加权限
{% highlight xml %}
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>
{% endhighlight %}
## 实现代码

{% highlight java %}
private void toggleSpeaker(Context context){
    AudioManager am = (AudioManager) context.getSystemService(Context.AUDIO_SERVICE);
    am.setMode(AudioManager.MODE_IN_CALL);
    am.setSpeakerphoneOn(!am.isSpeakerphoneOn());//取反赋值
}
{% endhighlight %}

