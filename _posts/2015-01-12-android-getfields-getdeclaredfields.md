---
layout: post
title:  "Java 反射机制getFields和getDeclaredFields区别"
date:   2015-01-12
image: password.jpg
description: getFields和getDeclaredFields傻傻分不清
categories: [android,java]
tags: [android, java, getFields, getDeclaredFields]

---

## 主要区别
- getFields()获取的是类的公共public字段，包括父类
- getDeclaredFields()获取类所有申明的字段，包括public、private、和proteced,但不包括父类所申明的字段所以要想获得父类的字段，就必须把所有的字段都设成public，实在蛋疼…..

13日补充 @TERRY

从这个 stack overflow的问题 发现这种不错的办法，看起来挺可行的。
{% highlight java %}
public static List<Field> getAllFields(Class<?> type) {
    List<Field> fields = new ArrayList<Field>();
    for (Class<?> c = type; c != null; c = c.getSuperclass()) {
        fields.addAll(Arrays.asList(c.getDeclaredFields()));
    }
    return fields;
}
{% endhighlight %}

