---
layout: post
title:  "Java 经验法则2 参数较多时考虑用构建器代替构造方法"
date:   2013-11-15
image: password.jpg
description: 用构建器解决构造多参数对象问题
categories: [android, java]
tags: [android, java, builder, constructor]

---


## 静态工厂方法和构造方法局限性
它们都不能很好地扩展到大量可选参数。

举个栗子：用一个类表示包装食品外面显示的营养成分标签。这些标签中有几个是必须的：每份的含量、每罐的含量以及每份的卡路里，还有超过20个可选参数：总脂肪量、饱和脂肪量、胆固醇等。

对于这样的类如果采用重叠构造方法模式，将会很繁琐也不便使用和读懂。

{% highlight java %}
//当有大量的参数时这将会变得难以控制，也不好修改
public class NutritionFacts{
   private final int servingSize;  //必选
   private final int servings;     //必选
   private final int calories;     //可选
   private final int fat;          //可选
   private final int sodium;       //可选
   private final int carbohydrate; //可选
    
 
   public NutritionFacts(int servingSize,int servings){
       this(servingSize,servings,0);
   }
 
   public NutritionFacts(int servingSize,int servings,int calories){
       this(servingSize,servings,calories,0);
   }
 
   public NutritionFacts(int servingSize,int servings,int calories,int fat){
       this(servingSize,servings,calories,fat,0);
   }
 
   public NutritionFacts(int servingSize,int servings,int calories,int fat,int sodium){
       this(servingSize,servings,calories,fat,sodium,0);
   }
 
   public NutritionFacts(int servingSize,int servings,int calories,int fat,int sodium,
                 int carbohydrate){
       this.servingSize = servingSize;
       this.servings = servings;
       this.calories = calories;
       this.fat = fat;
       this.sodium = sodium;
       this.carbohydrate = carbohydrate;
   }
}
{% endhighlight %}

## 构建器模式(Builder Mode)
构建器不直接生成想要的对象，而是让客户端利用所有必要的参数调用构造方法或静态工厂方法，得到一个builder对象，然后客户端在builder对象上调用类似于setter的方法，来设置每个相关的可选参数，最后，客户端调用无参的build方法来生成一个不可变得对象。


{% highlight java %}
public class NutritionFacts{
 
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;
 
    //内部构建器
    public static class Builder{
        //必选参数
        private final int servingSize;
        private final int servings;
         
        //可选参数
        private int calories = 0;
        private int fat = 0;
        private int carbohydrate = 0;
        private int sodium = 0;
 
        public Builder(int servingSize,int servings){
            this.servingSize = servingSize;
            this.servings = servings;
        }
 
        public Builder calories(int val){
            this.calories = val;
            return this;
        }
 
        public Builder fat(int val){
            this.fat = val;
            return this;
        }
 
        public Builder carbohydrate(int val){
            this.carbohydrate = val;
            return this;
        }
 
        public Builder sodium(int val){
            this.sodium = val;
            return this;
        }
 
        public NutritionFacts build(){
            return new NutritionFacts(this);
        }
    }
 
    private NutritionFacts(Builder builder){
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
{% endhighlight %}

NutritionFacts是不可变的,所有的默认参数值都是单独放在一个地方，builder的setter方法返回Builder本身，以便可以把调用链接起来，以下是调用方法

{% highlight java %}
NutritionFacts nut = 
    new NutritionFacts.Builder(200,8)
        .calories(100)
        .sodium(34)
        .build();
{% endhighlight %}

Builder模式十分灵活，可以利用单个builder构建多个对象，builder的参数可以在创建对象期间进行调整，也可以随着不同的对象而改变。builder可以自动填充某些参数，例如每次创建对象时自动增加序号。

设置了参数的builder生成了一个很好地抽象工厂(Abstract Factory)，客户端可以将这样一个builder传给方法，使该方法能够为客户端创建一个或多个对象。要使用这种用法需要有个类型来表示builder。在1.5版本以后，只要一个泛型就能满足所有的builder，无论它们在构建哪种类型的对象：

{% highlight java %}
public interface Builder<T>{
    public T build();
}
{% endhighlight %}