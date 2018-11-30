---
layout: post
title:  "Android Json数据解析处理"
date:   2013-08-14
image: json.jpg
description: 常用的Json解析方法：JsonObject, Gson, FastJson
category: android
tags: [android, gson, fastjson, jsonobject, parse]

---

Json 是一种轻量级的数据交换格式。采用完全独立的语言文本格式，易于人阅读和编写，同时也易于机器解析和生成。android开发中也常常用到Json传输数据，下面简单介绍几种解析方法。


#### 待解析的Json数据 jsonStr
{% highlight json %}
{
  "id":0,
  "name":"初三一班",
  "students":[
     {"name":"guest","id":2},
     {"name":"root","id":3}
  ]
}
{% endhighlight %}

#### 通过JsonObject
这里只做简单的用法，不涉及数据的封装。注意捕获异常！
{% highlight java %}

JSONObject clazzObj = new JSONObject(jsonStr);
int clazzId = clazzObj.getInt("id");
String clazzName = clazzObj.getString("name");
JSONArray stuArray = clazzObj.getJSONArray("students");
for (int i = 0; i < stuArray.length(); i++) {
  JSONObject stuObj = stuArray.getJSONObject(i);
  int stuId = stuObj.getInt("id");
  String stuName = stuObj.getString("name");
}        

{% endhighlight %}


#### 通过Gson来解析并转换成数据实体类
Gson 是google开发的一个解析库，能实现Json数据与数据实体类相互转换

##### 学生实体类

{% highlight java %}
public class Student{
  private Long id;
  private String name;

  //这里省略了setter和getter方法

}
{% endhighlight %}

##### 班级实体类

{% highlight java %}
public class Clazz{
  private Long id;
  private String name;
  private List<Student> students;

  //这里省略了setter和getter方法

}
{% endhighlight %}

##### 将数据转换成实体类
{% highlight java %}

Gson gson = new Gson();
Clazz clazz = gson.fromJson(jsonStr,Clazz.class);

{% endhighlight %}

##### 将实体类转换成Json
{% highlight java %}

Gson gson = new Gson();
String jsonStr = gson.toJson(clazz);

{% endhighlight %}


#### 通过FastJson来解析并转换成数据实体类
阿里推出的一款快速解析Json类库,功能用法跟Gson相似，支持多语言多平台，感兴趣的可去官网看看

FastJson [官网地址](http://www.codeproject.com/Articles/159450/fastJSON)

{% highlight java %}
Clazz clazz = JSON.parseObject(jsonStr,Clazz.class);
{% endhighlight %}









