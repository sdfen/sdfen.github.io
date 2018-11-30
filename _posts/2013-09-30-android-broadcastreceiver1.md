---
layout: post
title:  "Android BroadcastReceiver 1 广播简介与使用方法"
date:   2013-09-30
image: password.jpg
description: 广播的简单使用
category: android
tags: [android, BroadcastReceiver, onReceive]

---


[官方帮助文档](http://developer.android.com/reference/android/content/BroadcastReceiver.html)

要创建自己的BroadcastReceiver对象，我们需要继承android.content.BroadcastReceiver，并实现其onReceive方法。下面我们就创建一个名为MyReceiver广播接收者：

{% highlight java %}
public class MyReceiver extends BroadcastReceiver {
  private static final String TAG = "MyReceiver";
  @Override
  public void onReceive(Context context, Intent intent) {
    String msg = intent.getStringExtra("msg");
    Log.i(TAG, msg);
  }
}
{% endhighlight %}

在onReceive方法内，我们可以获取随广播而来的Intent中的数据，这非常重要，就像无线电一样，包含很多有用的信息。

在创建完我们的BroadcastReceiver之后，还不能够使它进入工作状态，我们需要为它注册一个指定的广播地址。没有注册广播地址的BroadcastReceiver就像一个缺少选台按钮的收音机，虽然功能俱备，但也无法收到电台的信号。下面我们就来介绍一下如何为BroadcastReceiver注册广播地址。

#### 静态注册

静态注册是在AndroidManifest.xml文件中配置的，我们就来为MyReceiver注册一个广播地址

{% highlight xml %}
<receiver android:name=".MyReceiver">
  <intent-filter>
    <action android:name="android.intent.action.MY_BROADCAST"/>
    <category android:name="android.intent.category.DEFAULT" />
  </intent-filter>
</receiver>
{% endhighlight %}

配置了以上信息之后，只要是android.intent.action.MY_BROADCAST这个地址的广播，MyReceiver都能够接收的到。注意，这种方式的注册是常驻型的，也就是说当应用关闭后，如果有广播信息传来，MyReceiver也会被系统调用而自动运行。

#### 动态注册
动态注册需要在代码中动态的指定广播地址并注册，通常我们是在Activity或Service注册一个广播，下面我们就来看一下注册的代码

{% highlight java %}
MyReceiver receiver = new MyReceiver();
IntentFilter filter = new IntentFilter();
filter.addAction("android.intent.action.MY_BROADCAST");
registerReceiver(receiver, filter);
{% endhighlight %}

注意，registerReceiver是android.content.ContextWrapper类中的方法，Activity和Service都继承了ContextWrapper，所以可以直接调用。在实际应用中，我们在Activity或Service中注册了一个BroadcastReceiver，当这个Activity或Service被销毁时如果没有解除注册，系统会报一个异常，提示我们是否忘记解除注册了。所以，记得在特定的地方执行解除注册操作

{% highlight java %}
@Override
protected void onDestroy() {
  super.onDestroy();
  unregisterReceiver(receiver);
}
{% endhighlight %}

执行这样行代码就可以解决问题了。注意，这种注册方式与静态注册相反，不是常驻型的，也就是说广播会跟随程序的生命周期。

我们可以根据以上任意一种方法完成注册，当注册完成之后，这个接收者就可以正常工作了。我们可以用以下方式向其发送一条广播

{% highlight java %}
public void send(View view) {
  Intent intent = new Intent("android.intent.action.MY_BROADCAST");
  intent.putExtra("msg", "hello receiver.");
  sendBroadcast(intent);
}
{% endhighlight %}

注意，sendBroadcast也是android.content.ContextWrapper类中的方法，它可以将一个指定地址和参数信息的Intent对象以广播的形式发送出去。

上面的例子只是一个接收者来接收广播，如果有多个接收者都注册了相同的广播地址，又会是什么情况呢，能同时接收到同一条广播吗，相互之间会不会有干扰呢？这就涉及到普通广播和有序广播的概念了。


#### 普通广播（Normal Broadcast）

普通广播对于多个接收者来说是完全异步的，通常每个接收者都无需等待即可以接收到广播，接收者相互之间不会有影响。对于这种广播，接收者无法终止广播，即无法阻止其他接收者的接收动作。

为了验证以上论断，我们新建三个BroadcastReceiver，演示一下这个过程，FirstReceiver、SecondReceiver和ThirdReceiver的代码如下

{% highlight java %}
//第一个接收者
public class FirstReceiver extends BroadcastReceiver {
  private static final String TAG = "NormalBroadcast";
  @Override
  public void onReceive(Context context, Intent intent) {
    String msg = intent.getStringExtra("msg");
    Log.i(TAG, "FirstReceiver: " + msg);
  }
}
 
//第二个接收者
public class SecondReceiver extends BroadcastReceiver {
  private static final String TAG = "NormalBroadcast";
  @Override
  public void onReceive(Context context, Intent intent) {
    String msg = intent.getStringExtra("msg");
    Log.i(TAG, "SecondReceiver: " + msg);
  }
}
 
//第三个接收者
public class ThirdReceiver extends BroadcastReceiver {
  private static final String TAG = "NormalBroadcast";
  @Override
  public void onReceive(Context context, Intent intent) {
    String msg = intent.getStringExtra("msg");
    Log.i(TAG, "ThirdReceiver: " + msg);
  }
}
{% endhighlight %}

然后再次点击发送按钮，发送一条广播，控制台打印如下

{% highlight java %}
FirstReceiver: Hello!
SecondReceiver: Hello!
ThirdReceiver: Hello!
{% endhighlight %}

看来这三个接收者都接收到这条广播了，我们稍微修改一下三个接收者，在onReceive方法的最后一行添加以下代码，试图终止广播

{% highlight java %}
//终止广播
abortBroadcast();
{% endhighlight %}

再次点击发送按钮，我们会发现，控制台中三个接收者仍然都打印了自己的日志，表明接收者并不能终止广播.


#### 有序广播（Ordered Broadcast）
有序广播比较特殊，它每次只发送到优先级较高的接收者那里，然后由优先级高的接受者再传播到优先级低的接收者那里，优先级高的接收者有能力终止这个广播。

为了演示有序广播的流程，我们修改一下上面三个接收者的代码，如下

{% highlight java %}
//第一个接受者
public class FirstReceiver extends BroadcastReceiver {
  private static final String TAG = "OrderedBroadcast";
  @Override
  public void onReceive(Context context, Intent intent) {
    String msg = intent.getStringExtra("msg");
    Log.i(TAG, "FirstReceiver: " + msg);
    Bundle bundle = new Bundle();
    bundle.putString("msg", msg + "@FirstReceiver");
    setResultExtras(bundle);
  }
}
 
//第二个接受者
public class SecondReceiver extends BroadcastReceiver {
  private static final String TAG = "OrderedBroadcast";
  @Override
  public void onReceive(Context context, Intent intent) {
    String msg = getResultExtras(true).getString("msg");
    Log.i(TAG, "SecondReceiver: " + msg);
    Bundle bundle = new Bundle();
    bundle.putString("msg", msg + "@SecondReceiver");
    setResultExtras(bundle);
  }
}
 
//第三个接受者
public class ThirdReceiver extends BroadcastReceiver {
  private static final String TAG = "OrderedBroadcast";
  @Override
  public void onReceive(Context context, Intent intent) {
    String msg = getResultExtras(true).getString("msg");
    Log.i(TAG, "ThirdReceiver: " + msg);
  }
}
{% endhighlight %}

我们注意到，在FirstReceiver和SecondReceiver中最后都使用了setResultExtras方法将一个Bundle对象设置为结果集对象，传递到下一个接收者那里，这样以来，优先级低的接收者可以用getResultExtras获取到最新的经过处理的信息集合。
代码改完之后，我们需要为三个接收者注册广播地址，我们修改一下AndroidMainfest.xml文件

{% highlight xml %}
<receiver android:name=".FirstReceiver">
  <intent-filter android:priority="1000">
    <action android:name="android.intent.action.MY_BROADCAST"/>
    <category android:name="android.intent.category.DEFAULT" />
  </intent-filter>
</receiver>
<receiver android:name=".SecondReceiver">
  <intent-filter android:priority="999">
    <action android:name="android.intent.action.MY_BROADCAST"/>
    <category android:name="android.intent.category.DEFAULT" />
  </intent-filter>
</receiver>
<receiver android:name=".ThirdReceiver">
  <intent-filter android:priority="998">
    <action android:name="android.intent.action.MY_BROADCAST"/>
    <category android:name="android.intent.category.DEFAULT" />
  </intent-filter>
</receiver>
{% endhighlight %}

我们看到，现在这三个接收者的<intent-filter>多了一个android:priority属性，并且依次减小。这个属性的范围在-1000到1000，数值越大，优先级越高。
现在，我们需要修改一下发送广播的代码，如下

{% highlight java %}
public void send(View view) {
  Intent intent = new Intent("android.intent.action.MY_BROADCAST");
  intent.putExtra("msg", "hello receiver.");
  sendOrderedBroadcast(intent, "scott.permission.MY_BROADCAST_PERMISSION");
}
{% endhighlight %}

注意，使用sendOrderedBroadcast方法发送有序广播时，需要一个权限参数，如果为null则表示不要求接收者声明指定的权限，如果不为null，则表示接收者若要接收此广播，需声明指定权限。这样做是从安全角度考虑的，例如系统的短信就是有序广播的形式，一个应用可能是具有拦截垃圾短信的功能，当短信到来时它可以先接受到短信广播，必要时终止广播传递，这样的软件就必须声明接收短信的权限。
所以我们在AndroidMainfest.xml中定义一个权限

{% highlight xml %}
<permission
  android:protectionLevel="normal" 
  android:name="scott.permission.MY_BROADCAST_PERMISSION" />
{% endhighlight %}

然后声明使用了此权限

{% highlight xml %}
<uses-permission 
  android:name="scott.permission.MY_BROADCAST_PERMISSION" />
{% endhighlight %}

然后我们点击发送按钮发送一条广播，控制台打印如下

{% highlight java %}
FirstReceiver: Hello
SecondReceiver: Hello@FirstReceiver
ThirdReceiver: Hello@FirstReceiver@SecondReceiver
{% endhighlight %}

我们看到接收是按照顺序的，第一个和第二个都在结果集中加入了自己的标记，并且向优先级低的接收者传递下去。

既然是顺序传递，试着终止这种传递，看一看效果如何，我们修改FirstReceiver的代码，在onReceive的最后一行添加以下代码

{% highlight java %}
//在firstReceiver的onReceiver中终止广播
abortBroadcast();
{% endhighlight %}

然后再次运行程序，控制台打印如下：

{% highlight java %}
FirstReceiver: Hello
{% endhighlight %}

此次，只有第一个接收者执行了，其它两个都没能执行，因为广播被第一个接收者终止了。

BroadcastReceiver的介绍完毕





 
#### 未完待续...





