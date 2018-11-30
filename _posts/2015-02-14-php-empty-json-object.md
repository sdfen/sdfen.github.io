---
layout: post
title:  "PHP 定义一个空Json对象"
date:   2015-02-14
image: password.jpg
description: json数据返回空对象
category: php
tags: [php,object, json, array, json_encode]

---

在写接口的过程当中，手机端有需求当没有数据时返回一个空对象。

一般我们会这样做

{% highlight php %}
$result=array();
echo json_encode($result);
{% endhighlight %}

但是得到的结果会是一个空数组，很正常，array就是定义数组嘛…

`[]`

其实很简单，只要强制转换即可。

{% highlight php %}
$result=(object)array();
//或者
//$result=(object)null;
 
echo json_encode($result);
{% endhighlight %}

返回结果

`{}`
