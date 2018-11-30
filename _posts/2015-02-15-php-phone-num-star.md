---
layout: post
title:  "PHP 用星号替换手机号码中间4位"
date:   2015-02-15
image: password.jpg
description: 手机号中间显示星号
category: php
tags: [php, substr_replace, preg_match]

---

直接用substr_replace替换

{% highlight php %}
$num = "13966778888"
$str = substr_replace($num,'****',3,4);
{% endhighlight %}

通过正则替换

{% highlight php %}
function hidtel($phone){
    $IsWhat = preg_match('/(0[0-9]{2,3}[-]?[2-9][0-9]{6,7}[-]?[0-9]?)/i',$phone); //固定电话
    if($IsWhat == 1){
        return preg_replace('/(0[0-9]{2,3}[-]?[2-9])[0-9]{3,4}([0-9]{3}[-]?[0-9]?)/i','$1****$2',$phone);
    }else{
        return  preg_replace('/(1[358]{1}[0-9])[0-9]{4}([0-9]{4})/i','$1****$2',$phone);
    }
}
{% endhighlight %}
