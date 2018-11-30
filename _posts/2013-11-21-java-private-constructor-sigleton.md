---
layout: post
title:  "Java 经验法则3 用私有构造方法或枚举类型强化Sigleton属性"
date:   2013-11-21
image: password.jpg
description: 私有构造函数对单例来说还是很重要的
categories: [android, java]
tags: [android, java, sigleton, constructor, 单例, 枚举, 私有构造方法]

---



## 单例
Singleton指仅仅被实例化一次的类。也就传说中得单例。

一般实现Singleton方法如下：

饿汉模式1

{% highlight java %}
public class Elvis{
    public static final Elvis INSTANCE = new Elvis();
    private Elvis(){
        //...
    }
    public void sayHello(){...}
 
}
{% endhighlight %}


饿汉模式2(使用静态工厂方法访问，推荐)
{% highlight java %}
public class Elvis{
    private static final Elvis INSTANCE = new Elvis();
    private Elvis(){
        //...
    }
 
    public void sayHello(){...}
 
    public static Elvis getInstance(){
        return INSTANCE;
    }
}
{% endhighlight %}

饿汉模式3
{% highlight java %}
public class Elvis{
    private Elvis INSTANCE = null;
    static{
        INSTANCE = new Elvis();
    }
    private Elvis(){
        //...
    }
 
    public void sayHello(){...}
 
    public static Elvis getInstance(){
        return INSTANCE;
    }
}
{% endhighlight %}


懒汉模式1(延迟加载,线程不安全)
{% highlight java %}
public class Elvis{
    private static Elvis INSTANCE = null;
    private Elvis(){
        //...
    }
 
    public void sayHello(){...}
 
    public static Elvis getInstance(){
        if(INSTANCE == null){
            INSTANCE = new Elvis();
        }
        return INSTANCE;
    }
}
{% endhighlight %}


懒汉模式2(延迟加载,线程安全,效率略低)
{% highlight java %}
public class Elvis{
    private static Elvis INSTANCE = null;
    private Elvis(){
        //...
    }
 
    public void sayHello(){...}
 
    public static synchronized Elvis getInstance(){
        if(INSTANCE == null){
            INSTANCE = new Elvis();
        }
        return INSTANCE;
    }
}
{% endhighlight %}


静态内部类(延迟加载，线程安全，推荐)
{% highlight java %}
public class Elvis{
    private static class SingletonHolder{
        private static final Elvis INSTANCE = new Elvis();
    }
    private Elvis(){
        //...
    }
 
    public void sayHello(){...}
 
    public static final Elvis getInstance(){
        return SingletonHolder.INSTANCE;
    }
}
{% endhighlight %}


单元素枚举类型(线程安全，无偿序列化，防止反射攻击,极力推荐)
{% highlight java %}
public enum Elvis{
    INSTANCE;
 
    private Elvis(){...}
 
    public void sayHello(){...}
}
{% endhighlight %}

注：除了枚举方式实现，以上方法实现Singleton的类序列化需要以下操作

1. implements Serializable
2. 加入以下方法
{% highlight java %}
private Object readResolve(){
    return INSTANCE;
}
{% endhighlight %}


## 枚举Singleton优势

1. 自由序列化；
2. 保证只有一个实例（即使使用反射机制也无法多次实例化一个枚举量）；
3. 线程安全

## 枚举Singleton缺点
1. 失去了一些类特性
2. 不能延迟加载