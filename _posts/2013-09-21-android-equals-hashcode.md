---
layout: post
title:  "Android hashcode() 和 equals()作用详解"
date:   2013-09-21
image: password.jpg
description: 重写hashcode和equals方法来自定义对象比较方式
category: android
tags: [android, equals, hashcode, instanceof]

---


#### equals()详解

##### Object类中的定义
{% highlight java %}
public boolean equals(Object obj) {
  return (this == obj); 
}
{% endhighlight %}

很明显，equals默认是根据地址值（引用）来比较两对象，而Integer、String等类已经重写了Object类的equals()方法，所以可以进行内容的比较。

##### equals方法重写原则

* 自反性：x.equals(x)必定返回true
* 对称性：x.equals(y)为true,那么y.equals(x)也为true
* 传递性：x.equals(y)为true,y.equals(z)为true,那么x.equals(z)也为true
* 一致性：x.equals(y)为true,无论调用多少次都为true,所以equals方法不要依赖不可靠的资源 如通过ip地址判断URL的相等性
* x.equals(null)必为false; x.equals(与x不同类型对象)，必为false

##### 编写高质量的equals方法步骤
1. if(obj == this) return true； 高性能
2. instanceof 校验对象类型正确性
3. 将参数转型到正确的类型
4. 对类中的每一个重要的属性跟目标对象进行校验
5. 针对实现验证是否对称，传递和一致

##### 例子

{% highlight java %}
@Override
public boolean equals(Object o) {
  //1.引用一样，说明是同一个对象，就没必要执行下面判断
  if (this == o) {  
    return true;
  }
  //2.校验对象类型正确性，不一样就不用在比较下面的
  if (!(o instanceof ApplicationInfo)) { 
    return false;
  }
  //3.将参数转型到正确的类型；
  ApplicationInfo that = (ApplicationInfo) o; 

  //对类中的每一个重要的属性跟目标对象进行校验
  return title.equals(that.title) &&
      intent.getComponent().getClassName().equals(
                    that.intent.getComponent().getClassName());
}
{% endhighlight %}


 
#### hashCode()详解
想要明白hashCode的作用，你必须要先知道Java中的集合。Java中的集合（Collection）有两类，一类是List，再有一类是Set。前者集合内的元素是有序的，元素可以重复；后者元素无序，但元素不可重复。这就存在一个问题：要想保证元素不重复，可两个元素是否重复应该依据什么来判断呢？这就用到上面的equals()方法，但是，如果每增加一个元素就检查一次，那么当元素很多时，后添加到集合中的元素比较的次数就非常多了。例如，如果集合中现在已经有1000个元素，那么第1001个元素加入集合时，它就要调用1000次equals方法。这显然会大大降低效率。因此就用到hashCode方法。

hashCode方法返回的就是对象存储的物理地址(实际可能并不是)，这样，当集合添加新元素时，先调用这个元素的hashCode方法，就能一下子定位到它应该放置的位置上，如果这个位置上没有元素，它可以直接存储在这个位置上，不用再进行任何比较；但如果这位置上已经有元素，就会调用它的equals与新元素进行比较，相同就不存了，不相同就放置到其他位置上，这样就降低了检索时间，提高效率；

##### hashCode()规定

1. equals()相等的两个对象，hashCode()一定相等
2. hashCode()相等的两个对象，equals()不一定相等

##### 例子

{% highlight java %}
//学生类，没有重写equals和hashCode
public class Student {
  public int id;
  public String name;

  public Student(int id,String name){
    this.id = id;
    this.name = name;
  }
}
{% endhighlight %}

把对象添加到HashSet

{% highlight java %}
HashSet clazz = new HashSet();
clazz.add(new Student(1,"Tony"));
clazz.add(new Student(2,"Jack"));
clazz.add(new Student(1,"Tony"));
{% endhighlight %}

遍历clazz输出结果：
{% highlight java %}
1 : Tony
2 : Jack
1 : Tony
{% endhighlight %}

显然hashSet添加了相同的学生，hashCode()比较的是对象地址（引用），所以两个new出来的hasCode是不一样，当然此时equals也是不等的。接下来重写equals和hashcode：

{% highlight java %}
@Override
public boolean equals(Object obj) {
  if(this == obj){
    return true;
  }
  if(!(obj instanceof Student)){
    return false;
  }
  Student that = (Student) obj;
  return this.id==that.id &&
         this.name.equals(that.name);
}

@Override
public int hashCode() {
  return id+name.hashCode();
}
{% endhighlight %}

输出结果：

{% highlight java %}
1 : Tony
2 : Jack
{% endhighlight %}

#### 完...





