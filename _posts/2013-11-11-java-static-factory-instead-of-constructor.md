---
layout: post
title:  "Java 经验法则1 考虑用静态工厂方法代替构造方法"
date:   2013-11-11
image: password.jpg
description: 静态工厂方法代替构造方法
categories: [android, java]
tags: [android, java, static, factory]

---


## 常规创建实例
为了让客户端获取它自身的一个实例，最常用的方法就是提供一个公有的构造方法。
例如

{% highlight java %}
public class Book {
 
    public Book(){
        //相关代码
    }
}
{% endhighlight %}

## 静态工厂方法创建实例(建议)
类可以提供一个公有的静态工厂方法 (不同于设计模式中的工厂方法模式)，它只是一个返回类的实例的静态方法

例如Boolean包装类：

{% highlight java %}
public class Boolean{
    //....
    public static Boolean valueOf(boolean b){
        return b? Boolean.TRUE : Boolean.FALSE;
    }
}
{% endhighlight %}


## 静态工厂方法相对于构造方法的优势

1. 静态工厂方法有名称
如果构造方法的参数本身没有确切的描述正被返回的对象，那么具有适当名称的静态工厂会更容易使用，代码更易于阅读和理解。例如，构造方法BigInteger(int,int,Random) 返回的BigInteger可能为素数，如果用名为BigInteger.probablePrime的静态工厂方法来表示，显然更为清楚。
一个类只能有一个带有指定签名的构造方法(方法的签名由它的名称和所有参数类型组成)，为了避免该限制，可以提供两个构造器，它们的参数列表只在参数类型的顺序上有所不同。但这并不是一个好主意，这会造成混乱，其他调用者永远也记不住该用哪个构造器，结果常常会调用错误的构造器。而由于静态工厂方法有名称，所以不受上述限制，当一个类需要多个带有相同签名的构造方法时，就用静态工厂方法代替构造方法，并慎重地选择名称以便突出它们之间的区别。

2. 静态工厂方法不用在每次调用它们的时候都创建一个新对象
这使得不可变类可以使用预先构建好的实例，或者将构建好的实例缓存起来，进行重复利用，从而避免创建不必要的重复对象。Boolean.valueOf(boolean)很好说明该特点，它从来不创建对象。

3. 静态工厂方法可以返回原返回类型的任何子类型的对象。

4. 在创建参数化类型实例时，它们使代码更简洁

{% highlight java %}
//繁琐的代码
Map<String, List<String>> m = new HashMap<String,List<String>>();
 
//假设HashMap提供以下静态工厂
public static <K, V> HashMap<K, V> newInstance(){
    return new HashMap<K, V>();
}
 
//那么声明就可以更简洁
Map<String, List<String>> m = HashMap.newInstance();
{% endhighlight %}


## 静态工厂方法缺点

1. 类如果不含公有或受保护的构造方法，就不能被子类化
2. 它们与其他的静态方法实际上没有任何区别

## 静态工厂方法一些惯用的名称
- valueOf – 不太严格的讲，该方法返回的实例与它的参数具有相同的值，这样的静态工厂方法实际上是类型转换方法
- of – valueOf的一种更为简洁的替代
- getInstance – 返回实例是通过方法的参数来描述的，但是不能说与参数具有同样地值，对应Singleton来说，该方法没有参数，并返回唯一实例。
- newInstance – 像getInstance一样，但newInstance能够确保返回的每个实例都与所有其他实例不同
- getType – 像getInstance一样，但是在工厂方法处于不同的类中的时候使用，Type表示工厂方法所返回的对象类型
- newType – 像newInstance一样，但是在工厂方法处于不同的类中的时候使用。

## 注意
方法签名：由名称和所有的参数类型组成，不包含返回类型

- Book buy(int,String) 与 void buy(int,String) 为相同签名，名称和参数列表一致
- Book buy(int) 与 Book buy(int,int) 为不同签名，参数列表不一致
- Book buy(int,String) 与 Book buy(String,int) 为不同签名，参数列表顺序不一样
