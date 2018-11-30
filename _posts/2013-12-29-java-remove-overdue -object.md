---
layout: post
title:  "Java 经验法则6 消除过期的对象引用"
date:   2013-12-29
image: password.jpg
description: 别让垃圾越积越多
categories: [android, java]
tags: [android, java, 过期引用]

---

java虽然有垃圾回收功能，但是并不那么智能，很多时候需要我们手动的去清理过期对象引用，以让垃圾回收更好好的工作。

看看下面栗子：

{% highlight java %}
//简单地栈实现
public class Stack{
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
 
    public Stack(){
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
 
    public void push(Object e){
        ensureCapacity();
        elements[size++] = e;
    }
 
    public Object pop(){
        if(size == 0){
            throw new EmptyStackException();
        }
        return elements[--size];
    }
 
    private void ensureCapacity(){
        if(elements.length == size){
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
 
}
{% endhighlight %}


以上实现并没有很明显的错误，无论如何测试，它都会成功通过每一项测试，但是这个程序隐藏着一个问题，随着内存占用不断增加，会导致内存泄露。

那么是怎样发生内存泄露呢？如果栈先增加，然后再收缩，那么从栈中弹出的对象将不会被当做垃圾回收，即时使用栈的程序不再引用这些对象，它们也不会被回收。这是因为，栈内部还维护着对这些对象的过期引用。所谓的过期引用是指永远不会被解除的引用。在本栗子里，凡是在elements数组的“活动部分”之外的任何引用都是过期的，活动部分是指elements中下标小于size的那些元素。

解决方法很简单只要在弹出时把引用也清空就好

{% highlight java %}
public Object pop(){
    if(size == 0){
        throw new EmptyStackException();
    }
    Object result = elements[--size];
    elements[size] = null;//清理引用
    return result;
}
{% endhighlight %}

但需要注意一点，清空对象的引用应该是一种例外，而不是一种规范行为。一般而言只要类是自己管理内存，程序猿就应该警惕内存泄露问题。

内存泄露的另一个常见的来源是缓存。一旦对象引用被放到缓存中，它就很容易被遗忘掉，从而使得它不再有用很长一段时间内仍然留在缓存中。解决方法是最好使用WeakHashMap来进行缓存，当缓存过期时或内存紧张时，它们会自动被删除。

内存泄露的第三个常见来源是监听器和其他回调。很多时候当我们注册了一下监听器或回调后就忘记了显式地去取消注册，这样它们往往会积聚起来，最终导致内存泄露，程序崩溃。