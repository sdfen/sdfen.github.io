---
layout: post
title:  "Java 经验法则9 复写equals时总要复写hashCode"
date:   2014-01-10
image: password.jpg
description: equals和hashCode是一对好基友
categories: [android, java]
tags: [android, java, equals, hashcode]

---

每个复写了equals的类中，也必须复写hashCode方法，如果不这样做的话，就会违反hashCode的通用约定，从而导致该类无法结合所有基于散列的集合一起正常工作，例如HashMap、HashSet和Hashtable。

下面是约定内容：

- 在应用执行期间，只要对象的equals方法没有被修改，那么对这同一个对象多次调用，hashCode方法都必须始终如一地返回同一个整数。在同一个应用程序多次执行过程中，每次执行所返回的整数可以不一致。
- 如果两个对象根据equals方法比较是相等的，那么两个对象的hashCode返回结果必须是相同的。
- 如果两个对象根据equals方法比较是不相等的，那么两个对象的hashCode返回结果可能是相同的，原则上要求是不相同的。

一个好的散列函数通常倾向于为不相等的对象产生不相等的散列码，这正是hashCode约定中第三条的含义，理想情况下，散列函数应该把集合中不相等的实例均匀地分布到所有可能的散列值上。

下面提供一种简单地计算散列值方法：

- 把某个非零的常数值，比如说17，保存到一个名为result的int类型变量中
- 对于对象中的每个关键属性f，完成一些操作
	- 如果属性是boolean类型，则计算c = (f?1:0)
	- 如果属性是byte、char、short、int类型，则计算c = (int)f
	- 如果属性是long类型，则计算c = (int)(f^(f>>>32))
	- 如果属性是float类型，则计算c = Float.floatToIntBits(f)
	- 如果属性是double类型，则计算Double.doubleToLongBits(f)，然后按照long类型处理计算
	- 如果属性是一个对象引用，则递归调用hashCode
	- 如果属性是一个数组，则要把每个元素当做单独的属性来处理，也就是说递归的应用上述规则，在1.5以上增加了一个Arrays.hashCode方法
	- 最后，把以上结果逐个用下面公式计算一遍：result = 31 * result + c; c为上述每条规则计算结果。
- 返回result
- 问问自己相等的实例是否都具有相等的散列码，要编写单元测试来验证你的推断。
{% highlight java %}
public class Person{
    public int id;
    public int age;
    public String name;
    public Person(int id,int age,String name){
        this.id = id;
        this.age = age;
        this.name = name;
    }
 
    @Override
    public boolean equals(Object o){
        //省略
    }
 
    @Override
    public int hashCode(){
        int result = 17;
        result = 31 * result + id;
        result = 31 * result + age;
        result = 31 * result + name.hashCode();
        return result;
    }
}
{% endhighlight %}


注：之所以选择31，是因为它是一个奇素数。如果乘数是偶数，并且乘法溢出的话，信息就丢失，因为与2相乘等价于移位运算。使用素数的好处并不很明显，但是习惯上都使用素数来计算散列结果。31有个很好地特性，即用移位和减法来代替乘法，可以得到更好地性能：31 * i == (i << 5) - i。现代的VM可以自动完成这种优化。

如果计算散列码开销比较大时，可以考虑把散列码缓存到对象内部，而不是每次重新计算。

{% highlight java %}
private volatile int hashCode;
 
@Override
public int hashCode(){
    int result = hashCode;
    if(result == 0){
        int result = 17;
        result = 31 * result + id;
        result = 31 * result + age;
        result = 31 * result + name.hashCode();
        hashCode = result;
    }
    return result;
}
{% endhighlight %}

