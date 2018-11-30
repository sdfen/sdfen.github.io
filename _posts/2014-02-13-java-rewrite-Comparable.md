---
layout: post
title:  "Java 经验法则12 考虑实现Comparable接口"
date:   2014-02-13
image: password.jpg
description: 对象排序时需要实现
categories: [android, java]
tags: [android, java, 排序, comparable]

---

Comparable的compareTo方法不但允许进行简单地等同性比较，而且允许执行顺序比较，除此之外，它与Object的equals方法具有相似的特征，它还是个泛型。类实现了Comparable接口，就表明它的实例具有内在的排序关系。

一旦类实现了Comparable接口，它就可以跟许多泛型算法以及依赖于该接口的集合进行协作。你付出很小的努力就可以获得非常强大的功能。事实上，Java平台类库的所有值类都实现了该接口。如果你正在编写一个值类，它具有非常明显的内在排序关系，比如按字母顺序，按数值顺序或按年代顺序，那你就应该坚决考虑实现这个接口：
{% highlight java %}
public interface Comparable<T>{
    int compareTo(T t);
}
{% endhighlight %}


compareTo的通俗约定与equals相似：

在下面说明中，符号sgn表示数学中的signum函数，它根据表达式的值为负值、零和正值，分别返回-1、0、1。

- 必须确保所有的x和y都满足sgn(x.compareTo(y)) == -sgn(y.compareTo(x))。这个也暗示着，如果y.compareTo(x)抛出异常时，那么x.compareTo(y)也必须抛出异常。

- 必须确保这个比较关系的可传递：(x.compareTo(y) > 0 && y.compareTo(z) > 0) 意味着x.compareTo(z) > 0。

- 必须确保x.compateTo(y) == 0 时，所有的z都满足：sgn(x.compareTo(z)) == sgn(y.compareTo(z))。

- 强烈建议(x.compareTo(y) == 0) == (x.equals(y)),但这并非绝对必要。一般说来任何实现了Comparable接口的类，若违反这个条件，都应该明确予以说明。推荐使用这样说法：注意，该类具有内在排序功能，但是与equals不一致

编写compareTo方法与编写equals方法非常相似，但也存在几处重大的差别。因为Comparable接口是参数化的，而且comparable方法是静态的类型，因此不必进行类型检查，也不必对他的参数进行类型转换。如果参数类型不合适，这个调用甚至无法编译。如果参数为null，这个调用应该抛出NullPointerException异常，并且一旦该方法试图访问它的成员时就应该抛出。

compareTo方法中的属性比较是顺序比较，而不是等同性的比较。比较对象引用属性可以是通过递归地调用compareTo方法实现。如果一个属性并没有实现Comparable接口，或者你需要使用一个非标准的排序关系，就可以使用一个显式的Comparator来代替。或者编写自己的Comparator，或者使用已有的Comparator，譬如针对第8条法则中CaseInsensitiveString类的这个compareTo方法使用一个已有的Compareator

{% highlight java %}
public final class CaseInsensitiveString implements Comparable<CaseInsensitiveString>{
     
    public int compareTo(CaseInsensitiveString cis){
        return String.CASE_INSENSITIVE_ORDER.compare(s,cis.s);
    }
}
{% endhighlight %}

如果一个类有多个关键属性，那么你必须从最关键的属性开始，逐步进行到所有的重要属性。如果某个属性的比较产生了非零的结果（零表示相等），则整个比较操作结束，并返回该结果。

