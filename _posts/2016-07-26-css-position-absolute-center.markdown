---
layout: post
title:  "html/css 通用布局水平垂直居中方法"
date:   2016-07-26
image: css.jpg
description: 解决position:absolute元素居中问题，兼容绝大部分浏览器
category: html
tags: [position, absolute, center, horizontal, vertical, html5, css3, javascript]
---


#### 一般子元素居中方法

Ps:宽度100%无效，对于position:absolute无效

<div style="width: 100%; height: 120px; padding: 10px; border: 2px solid black; margin:2em 0;">
<div style="width: 100px; height: 100px; margin: 0 auto; background: blue;"></div>
</div>

{% highlight css %}
.child-element {
    width: 100px;
    height: 100px;
    background: blue;
    margin: 0 auto;
}
{% endhighlight %}



#### 绝对布局子元素居中方法1
Ps:只限于子元素宽度固定时才有效

<div style="width: 100%; height: 120px; padding: 10px; border: 2px solid black; position: relative; margin: 2em 0;">
<div id="blueBox" style="width: 100px; height: 100px; margin: 0px auto; background-color: red; position: absolute; left: calc(50% - 50px); background-position: initial initial; background-repeat: initial initial;"></div>
</div>
<p><script type="text/javascript">
function changeBlueBoxWidth() {
var el = document.getElementById('blueBox');
if(el.style.width == '100px'){
  el.style.width = '200px';
} else {
  el.style.width = '100px';
}
}
</script></p>
<p>但当子元素宽度改变时，无法自适应：<a onclick="changeBlueBoxWidth();">点我改变宽度.</a></p>

{% highlight css %}
.child-element {
    position: absolute;
    width: 100px;
    height: 100px;
    left: calc(50% - 50px);
    background: red;
}
{% endhighlight %}

#### 绝对布局子元素居中方法2(自适应居中)(推荐)
Ps:position:relative也可以使用

<div style="width: 100%; height: 200px; padding: 10px; border: 2px solid black; position: relative; margin: 2em 0;">
<div id="vblueBox" style="padding: 0 10px; color: white; width:100px; height: auto; top: 50%; margin: 0 auto; background: green; position: absolute; left: 50%; -webkit-transform:translate(-50%, -50%); -moz-transform:translate(-50%,-50%); -o-transform:translate(-50%,-50%); -ms-transform:translate(-50%,-50%); transform:translate(-50%,-50%); ">
我是绝对布局，我可以自适应水平垂直居中
</div>
</div>
<p><script type="text/javascript">
function verticallyCenterBlueBox() {
var el = document.getElementById('vblueBox');
if(el.style.width == '100px'){
  el.style.width = '200px';
} else {
  el.style.width = '100px';
}
}
</script></p>
<p>现在当我们改变宽度时，也会自动居中： <a onclick="verticallyCenterBlueBox()">点我改变宽度</a>. </p>

{% highlight css %}
.child-element {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
}
{% endhighlight %}

<div style="width: 100%; height: 200px; padding: 10px; border: 2px solid black; position: relative; margin: 2em 0;">
<div id="v2blueBox" style="padding: 0 10px; color: black; width:100px; height: auto; top: 50%; background: orange; position: relative; left: 50%; -webkit-transform:translate(-50%, -50%); -moz-transform:translate(-50%,-50%); -o-transform:translate(-50%,-50%); -ms-transform:translate(-50%,-50%); transform:translate(-50%,-50%); ">
我是相对布局，我可以自适应垂直水平居中
</div>
</div>
<p><script type="text/javascript">
function vertically2CenterBlueBox() {
var el = document.getElementById('v2blueBox');
if(el.style.width == '100px'){
  el.style.width = '200px';
} else {
  el.style.width = '100px';
}
}
</script></p>
<p><a onclick="vertically2CenterBlueBox()">点我改变宽度</a>.</p>

{% highlight css %}
.child-element {
    position: relative;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
}
{% endhighlight %}

#### 适配各浏览器
{% highlight css %}
.child-element {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
    -ms-transform: translate(-50%,-50%); 	/* IE 9 */
    -moz-transform: translate(-50%,-50%); 	/* Firefox */
    -webkit-transform: translate(-50%,-50%); /* Safari 和 Chrome */
    -o-transform: translate(-50%,-50%); 	/* Opera */

}
{% endhighlight %}




#### 最后感谢大神:[joshbroton](http://joshbroton.com/absolute-positioning-and-horizontal-vertical-centering/)