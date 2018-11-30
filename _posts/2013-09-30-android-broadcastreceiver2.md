---
layout: post
title:  "Android BroadcastReceiver 2 常用系统广播"
date:   2013-09-30
image: password.jpg
description: 好玩的系统广播
category: android
tags: [android, BroadcastReceiver, onReceive]

---

## 开机启动服务
我们经常会有这样的应用场合，比如消息推送服务，需要实现开机启动的功能。要实现这个功能，我们就可以订阅系统“启动完成”这条广播，接收到这条广播后我们就可以启动自己的服务了。我们来看一下BootCompleteReceiver和MsgPushService的具体实现：


{% highlight java %}
//接收启动完成广播
public class BootCompleteReceiver extends BroadcastReceiver {
    private static final String TAG = "BootCompleteReceiver";
    @Override
    public void onReceive(Context context, Intent intent) {
        Intent service = new Intent(context, MsgPushService.class);
        context.startService(service);
        Log.i(TAG, "Boot Complete. Starting MsgPushService...");
    }
}
 
//Push服务
public class MsgPushService extends Service {
    private static final String TAG = "MsgPushService";
    @Override
    public void onCreate() {
        super.onCreate();
        Log.i(TAG, "onCreate called.");
    }
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.i(TAG, "onStartCommand called.");
        return super.onStartCommand(intent, flags, startId);
    }
    @Override
    public IBinder onBind(Intent arg0) {
        return null;
    }
}
{% endhighlight %}

然后我们需要在AndroidManifest.xml中配置相关信息：
{% highlight java %}
<!-- 开机广播接受者 -->
<receiver android:name=".BootCompleteReceiver">
    <intent-filter>
        <!-- 注册开机广播地址-->
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
<!-- 消息推送服务 -->
<service android:name=".MsgPushService"/>
{% endhighlight %}

我们看到BootCompleteReceiver注册了“android.intent.action.BOOT_COMPLETED”这个开机广播地址，从安全角度考虑，系统要求必须声明接收开机启动广播的权限，于是我们再声明使用下面的权限：
{% highlight xml %}
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
{% endhighlight %}


经过上面的几个步骤之后，我们就完成了开机启动的功能，将应用运行在模拟器上，然后重启模拟器，控制台打印如下：
onStartCommand called.

如果我们查看已运行的服务就会发现，MsgPushService已经运行起来了。




## 网络状态变化

在某些场合，比如用户浏览网络信息时，网络突然断开，我们要及时地提醒用户网络已断开。要实现这个功能，我们可以接收网络状态改变这样一条广播，当由连接状态变为断开状态时，系统就会发送一条广播，我们接收到之后，再通过网络的状态做出相应的操作。下面就来实现一下这个功能：


{% highlight java %}
public class NetworkStateReceiver extends BroadcastReceiver {
    private static final String TAG = "NetworkStateReceiver";
    @Override
    public void onReceive(Context context, Intent intent) {
        Log.i(TAG, "network state changed.");
        if (!isNetworkAvailable(context)) {
            Toast.makeText(context, "network disconnected!", 0).show();
        }
    }
 
    // 网络是否可用
    public static boolean isNetworkAvailable(Context context) {
        ConnectivityManager mgr = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo[] info = mgr.getAllNetworkInfo();
        if (info != null) {
            for (int i = 0; i < info.length; i++) {
                if (info[i].getState() == NetworkInfo.State.CONNECTED) {
                    return true;
                }
            }
        }
        return false;
    }
}
{% endhighlight %}

再注册一下这个接收者的信息：

{% highlight xml %}
<receiver android:name=".NetworkStateReceiver">
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
{% endhighlight %}

因为在isNetworkAvailable方法中我们使用到了网络状态相关的API，所以需要声明相关的权限才行，下面就是对应的权限声明：

{% highlight xml %}
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
{% endhighlight %}

我们可以测试一下，比如关闭WiFi，看看有什么效果

## 电量变化
如果我们阅读软件，可能是全屏阅读，这个时候用户就看不到剩余的电量，我们就可以为他们提供电量的信息。要想做到这一点，我们需要接收一条电量变化的广播，然后获取百分比信息，这听上去挺简单的，我们就来实现以下：

{% highlight java %}
public class BatteryChangedReceiver extends BroadcastReceiver {
    private static final String TAG = "BatteryChangedReceiver";
    @Override
    public void onReceive(Context context, Intent intent) {
        int currLevel = intent.getIntExtra(BatteryManager.EXTRA_LEVEL, 0); //当前电量
        int total = intent.getIntExtra(BatteryManager.EXTRA_SCALE, 1); //总电量
        int percent = currLevel * 100 / total;
        Log.i(TAG, "battery: " + percent + "%");
    }
}
{% endhighlight %}

然后再注册一下广播接地址信息就可以了：



{% highlight xml %}
<receiver android:name=".BatteryChangedReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BATTERY_CHANGED"/>
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
{% endhighlight %}

当然，有些时候我们是要立即获取电量的，而不是等电量变化的广播，比如当阅读软件打开时立即显示出电池电量。我们可以按以下方式获取：

{% highlight java %}
Intent batteryIntent = getApplicationContext().registerReceiver(null,new IntentFilter(Intent.ACTION_BATTERY_CHANGED));
int currLevel = batteryIntent.getIntExtra(BatteryManager.EXTRA_LEVEL, 0);
int total = batteryIntent.getIntExtra(BatteryManager.EXTRA_SCALE, 1);
int percent = currLevel * 100 / total;
Log.i("battery", "battery: " + percent + "%");
{% endhighlight %}
