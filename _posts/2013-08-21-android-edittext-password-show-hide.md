---
layout: post
title:  "Android EditText设置密码可见性"
date:   2013-08-21
image: password.jpg
description: 实现切换输入框里密码的可见性
category: android
tags: [android, EditText, setInputType, inputType, Password]

---
在实现登录注册页面等涉及到密码的输入框时，通常会给一个按钮来切换密码的可见性

#### 展示效果
<img src="{{ '/assets/img/posts/android-pwd-hide.jpg' | prepend: site.baseurl }}" alt=""> 

<img src="{{ '/assets/img/posts/android-pwd-show.jpg' | prepend: site.baseurl }}" alt=""> 
 
#### 代码实现
{% highlight java %}
if(isChecked){
  //editText:EditText
  //checkbox选中时明文显示密码
  editText.setInputType(
      InputType.TYPE_TEXT_VARIATION_VISIBLE_PASSWORD);
}else{
  //checkbox取消时隐藏具体密码
  editText.setInputType(
      InputType.TYPE_CLASS_TEXT | 
      InputType.TYPE_TEXT_VARIATION_PASSWORD);
}

{% endhighlight %}




