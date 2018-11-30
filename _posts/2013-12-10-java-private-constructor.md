---
layout: post
title:  "Java 经验法则4 通过私有构造方法强化不可实例化的能力"
date:   2013-12-10
image: password.jpg
description: 防止静态类被实例化
categories: [android, java]
tags: [android, java, sigleton, constructor, 私有构造方法]

---

有时候，需要编写只包含静态属性和静态方法的类，这样的工具类不希望被实例化，实例对它没有任何意义。为防止被无意实例化，这时需要显式的把构造方法设成私有的。
{% highlight java %}
public class Utils{
 
    //这里最后加段注释，说明一些原因
    private Utils(){
        throw new AssertionError();
    }
    //静态属性，静态方法...
 
}
{% endhighlight %}

