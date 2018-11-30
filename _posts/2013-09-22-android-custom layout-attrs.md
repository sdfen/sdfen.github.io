---
layout: post
title:  "Android 自定义布局属性"
date:   2013-09-22
image: password.jpg
description: 自定义控件时难免要添加一些自定义属性
category: android
tags: [android, custom, attrs, format]

---


#### 新建/res/values/attrs.xml
这里给一个CircleImageView的控件设置几个自定义属性

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <declare-styleable name="CircleImageView">
    <attr name="borderWidth" format="dimension" />
    <attr name="borderColor" format="color" />
    <attr name="cornersRadius" format="dimension" />
    </declare-styleable>
</resources>

{% endhighlight %}

format可定义数据类型

{% highlight xml %}
reference   //引用资源
color       //颜色值
boolean     //布尔值
dimension   //尺寸值
float       //浮点型
integer     //整型
string      //字符串
fraction    //百分数
enum        //枚举类型
flag        //位或运算
{% endhighlight %}


#### 在布局文件里使用
先声明命名空间 xmlns:attribute="http://schemas.android.com/apk/res/<app包名>" 或者 xmlns:attribute="http://schemas.android.com/apk/res-auto",其中attribute可以取除android以外任何字符串

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<cn.apphack.test.CircleImageView 
  xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:apphack="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  apphack:borderWidth="1dp"
  apphack:borderColor="#ff0000"
  apphack:cornersRadius="20dp" />
{% endhighlight %}




#### 代码里获取属性值

{% highlight java %}
class CircleImageView extends ImageView{
  ...
  public CircleImageView(Context context, AttributeSet attrs) {
    super(context, attrs);
    TypedArray a =
        context.obtainStyledAttributes(attrs,R.styleable.CircleImageView);
    mBorderWidth = a.getDimensionPixelSize(
        R.styleable.CircleImageView_borderWidth, 1);
    mBorderColor = a.getColor(
        R.styleable.CircleImageView_borderColor, Color.BLACK);
    mCornersRadius = a.getDimensionPixelSize(
        R.styleable.CircleImageView_cornersRadius, 10);
    a.recycle();
  }
}
{% endhighlight %}


 
#### 完...





