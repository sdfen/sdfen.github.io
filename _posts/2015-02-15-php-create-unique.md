---
layout: post
title:  "PHP 生成唯一标识id"
date:   2015-02-15
image: password.jpg
description: 唯一的id很多地方会用到，记录一下
category: php
tags: [php, uniqid, md5]

---

PHP倒是自带了生成唯一id的函数：uniqid() ，它是基于当前时间微秒数的

{% highlight php %}
echo uniqid(); //13位的字符串
echo uniqid("php_"); //当然你可以加上前缀
echo uniqid("php_", TRUE); //如果第二个参数more_entropy为true则生成23位字符串
{% endhighlight %}

但是它生成的标识有可能不是唯一的，所以很多人会

{% highlight php %}
//这是第一种简单的方法,当然用sha1()函数也可以。
echo md5(uniqid());
//第二种，利用时间戳的方法
echo md5(time().mt_rand(1,1000000));
{% endhighlight %}

如果你还担心，可以用更严谨的方法

{% highlight php %}
function create_unique() {   
    $data = $_SERVER['HTTP_USER_AGENT'] . $_SERVER['REMOTE_ADDR']   
    .time() . rand();   
    return sha1($data);   
    //return md5(time().$data);
}  
{% endhighlight %}
