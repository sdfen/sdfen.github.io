---
layout: post
title:  "Android 通话挂断实现"
date:   2014-09-08
image: password.jpg
description: 通话挂断实现代码，支持4.0+
category: android
tags: [android, endcall, ITelephony, NeighboringCellInfo]

---

## 导入AIDL文件
1. 在src下新建包：com.android.internal.telephony 和 android.telephony
2. 下载[ITelephony.aidl](https://pan.baidu.com/s/1qWwi1zA) 和 [NeighboringCellInfo.aidl](https://pan.baidu.com/s/1bneYcqF)
3. 拷贝`ITelephony.aidl`到`com.android.internal.telephony`包下，拷贝`NeighboringCellInfo.aidl`到`android.telephony`包下

## 权限

{% highlight xml %}
<uses-permission android:name="android.permission.CALL_PHONE"/>;
{% endhighlight %}

## 实现

{% highlight java %}
  private void endCall() {
    ITelephony iTelephony = null;
    TelephonyManager telMgr = (TelephonyManager)
            this.getSystemService(Service.TELEPHONY_SERVICE);
    Class<TelephonyManager> c = TelephonyManager.class;
    Method getITelephonyMethod = null;
    try {
        getITelephonyMethod = c.getDeclaredMethod("getITelephony", (Class[]) null);
        getITelephonyMethod.setAccessible(true);
    } catch (SecurityException e) {
        e.printStackTrace();
    } catch (NoSuchMethodException e) {
        e.printStackTrace();
    }
 
    try {
        iTelephony = (ITelephony)
                getITelephonyMethod.invoke(telMgr, (Object[]) null);
        iTelephony.endCall();
    } catch (IllegalArgumentException e) {
        e.printStackTrace();
    } catch (IllegalAccessException e) {
        e.printStackTrace();
    } catch (InvocationTargetException e) {
        e.printStackTrace();
    }catch (Exception e){
        e.printStackTrace();
    }
 
}
{% endhighlight %}

