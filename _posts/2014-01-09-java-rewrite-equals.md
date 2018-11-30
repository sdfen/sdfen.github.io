---
layout: post
title:  "Java 经验法则8 复写equals时请遵守通用约定"
date:   2014-01-09
image: password.jpg
description: equals复写并不是那么简单
categories: [android, java]
tags: [android, java, equals]

---

以下情况大可不必要复写equals:

1. 类的每个实例本质上都是唯一的(单例).
2. 不关心类是否提供了逻辑相等的测试功能。
3. 超类已经覆盖了equals,从超类继承过来的行为对于子类也合适。
4. 类是私有的或包级私有的，可以确定它的equals方法永远不会被调用。在此无疑是应该做以下处理以防被意外调用。
{% highlight java %}
@Override
public boolean equals(Object o){
    throw new AssertionError();
}
{% endhighlight %}


而如果类具有自己特有的逻辑相等概念，不同于对象等同概念，而且超类还没覆盖equals以实现期望行为，这时我们就需要覆盖equals方法。

复写equals必须遵守的通用约定

- 自反性：对于任何非空实例x,x.equals(x)必须返回true;
如果违反这条，然后把该类的实例添加到集合中，该集合的contains方法将果断的告诉你，该集合不包含你刚刚添加的实例。

- 对称性：对于任何非空实例x和y，如果x.equals(y)返回true,那么y.equals(x)也必须返回true。
若无意违反了这条，这种情形倒是不难想象。例如考虑下面的类，它实现了一个区分大小写的字符串。字符串由toString保存，但在比较操作中被忽略。

{% highlight java %}
public final class CaseInsensitiveString{
    private final String s;
     
    public CaseInsensitiveString(String s){
        if(s == null){
            throw new NullPointerException();
        }
        this.s = s;
    }
 
    @Override
    public boolean equals(Object o){
        if(o instanceof CaseInsensitiveString)
            return s.equalsIgnoreCase(((CaseInsensitiveString)o).s);
     
        if(o instanceof String)
            return s.equalsIgnoreCase((String) o);
 
        return false;
    }
}
{% endhighlight %}

假设我们有一个不区分大小写的字符串和一个普通字符串：
{% highlight java %}
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";
{% endhighlight %}

正如所料，cis.equals(s) 返回true，但问题在于，虽然CaseInsensitiveString类中的equals方法知道普通的字符串String对象，但是在String类中的equals方法却并不知道不区分到小写的字符串，因此，s.equals(cis)返回false,显然违反了对称性。

为了解决这个问题，只需要把企图与String互操作的这段代码从equals方法中去掉就可以了
{% highlight java %}
@Override
public boolean equals(Object o){
    return o instanceof CaseInsensitiveString && ((CaseInsensitivieString) o).s.equalsIgnoreCase(s);
}
{% endhighlight %}

- 传递性：对于任何非空实例x，y，z，只要x.equals(y)返回true，并且y.equals(z)也返回true,那么x.equals(z)也必须返回true。
- 
举个反面栗子：
{% highlight java %}
public class Point{
    private final int x;
    private final int y;
    public Point(int x, int y){
        this.x = x;
        this.y = y;
    }
 
    @Override
    public boolean equals(Object o){
        if(!(o instanceof Point))
            return false;
 
        Point p = (Point)o;
         
        return p.x == x && p.y == y;
    }
}
{% endhighlight %}

假设你想扩展这个类，为一个点添加颜色信息：
{% highlight java %}
public class ColorPoint extends Point{
    private final Color color;
    public ColorPoint(int x, int y, Color color){
        super(x,y);
        this.color = color;
    }
 
}
{% endhighlight %}

如果不提供equals方法，而是直接从Point继承过来，在equals做比较时颜色信息就会被忽略。虽然不会违反约定，但是很明显这是无法接受。假设你编写了一个equals方法，只有当它的参数是另一个有色点，并具有相同位置和颜色时才返回true

{% highlight java %}
@Override
public boolean equals(Object o){
    if(!(o instanceof ColorPoint))
        return false;
         
    return super.equals(o) && ((ColorPoint)o).color == color;
}
{% endhighlight %}

这个方法问题在于，你在比较普通点和有色点时，以及相反情形时，可能会得到不同的结果。前一种比较忽略了颜色信息，后一种比较则总返回false，因为参数的类型不正确。这违反了对称性。
{% highlight java %}
Point p = new Point(1,2);
ColorPoint cp = new ColorPoint(1,2,Color.RED);
{% endhighlight %}

显然，p.equals(cp)返回true,cp.equals(p)返回falsely。你可以尝试来修正这个问题，让ColorPoint.equals在进行混合比较时忽略颜色信息
{% highlight java %}
@Override
public boolean equals(Object o){
    if(!(o instanceof Point))
        return false;
     
    if(!(o instanceof ColorPoint))
        return o.equals(this);
 
    return super.equals(o) && ((ColorPoint)o).color == color;
}
{% endhighlight %}

这样做确实提供了对称性，但是却牺牲了传递性
{% highlight java %}
ColorPoint p1 = new ColorPoint(1,2,Color.RED);
Point p2 = new Point(1,2);
ColorPoint p3 = new ColorPoint(1,2,Color.BLUE);
{% endhighlight %}

此时，p1.equals(p2)和p2.equals(p3)都返回true ，但是p1.equals(p3)则返回false，显然违反了传递性，前两种比较不考虑颜色信息，而第三站比较则考虑颜色信息。

事实上，这是面向对象语言中关于等价关系的一个基本问题。我们无法在扩展可实例化的类的同时，既增加新的组件，同时又保留equals约定。

比较好的解决方法就是只要不可能直接创建超类实例，前面所述种种问题都不会发生。或者超类和子类不同时实例化并且使用。

- 一致性：对于任何非空实例x和y，只要equals的比较操作在对象中所用信息没有被修改，多次调用x.equals(y)就会一致的返回true，或者一致地返回false。

- 非空性：对于任何非空的实例x，x.equals(null)必须返回false。
许多类的equals方法通过一个显式的null检测来防止这种情况。
{% highlight java %}
@Override
public boolean equals(Object o){
    if(o == null)
        return false;
    ...
}
{% endhighlight %}

其实这项检测是不必要的。为了检测其参数的同等性，equals方法必须先把参数转成适当的类型，以便可以调用它的访问方法或者属性，在进行转换之前，还必须使用instanceof操作符来判断类型。

{% highlight java %}
@Override
public boolean equals(Object o){
    if(!(o instanceof MyType))
        return false;
 
    MyType mt = (MyType) o;
 
    ...
}
{% endhighlight %}

如果漏掉这一步那么有可能会抛出ClassCastException异常。但是如果传递进来的参数为null，那么不管instanceof 哪种类型，都会返回false，所以，可以不用单独的null检测。

高质量equals编写：

1. 使用==操作符检查参数是否为这个对象的引用
2. 使用instanceof操作符检查参数是否正确地类型。
3. 把参数转换成正确类型。
4. 对于该类每个关键属性都进行比较。

{% highlight java %}
public class Person{
    public int id;
    public Person(int id){
        this.id = id;
    }
 
    @Override
    public boolean equals(Object o){
        //1.
        if(this == o)
            return true;
 
        //2.
        if(!(o instanceof Person))
            return false;
 
        //3.
        Person p = (Person) o;
 
        //4.
        return p.id == id;
 
    }
}
{% endhighlight %}

注意：当包含很多关键属性时，在比较时应该最先比较最有可能不一致的属性，或者开销最低的属性。

编写完成后，应该问自己三个问题，是否对称的，是否传递的，是否一致的。

最后一些告诫：

1. 复写equals时总要复写hashCode
2. 不要企图让equals方法过于智能。
3. 不要将equals声明中的Object对象参数替换成其他的类型。

例如：
{% highlight java %}
//这是错误的,这不是复写了equals,而是重载了equals，在原equals基础上，再提供一个强类型equals方法。
public boolean equals(MyClass o){
    ...
}
{% endhighlight %}

为了避免上面错误，在方法前加上@Override注解就好，系统会给你检查。


