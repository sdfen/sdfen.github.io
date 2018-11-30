---
layout: post
title:  "ios html5添加图标到主屏幕"
date:   2016-07-28
image: iphone.jpg
description: 通过safari的添加到主屏幕，让你的web app更像native app
category: apple
tags: [ios, add-to-homescreen, html5]
---

#### 设配屏幕大小并且去掉safari的地址栏，状态栏
{% highlight html %}
<meta name="viewport" content="width=device-width,
		initial-scale=1,maximum-scale=1,user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black">
<meta name="format-detection" content="telephone=no">
{% endhighlight %}


#### 设置添加到主屏幕的图标
`apple-touch-icon` 与 `apple-touch-icon-precomposed`都可以设置图标，那么，多出来的`precomposed` 表示神马意思呢？没有`precomposed` 代码，一些包括圆角，阴影，反光的特效便会自动添加到生成的本地app 的logo 中。是自iOS 2.0 开始的，但如今随着iOS7 的出现，已经变得可有可无了;

<img src="{{ '/assets/img/posts/homescreen-icon.jpg' | prepend: site.baseurl }}" alt=""> 

{% highlight html %}
<!-- 自定义各种尺寸图标 ,这里偷懒一下就用一张 -->
<link rel="apple-touch-icon-precomposed" 
      sizes="144x144" href="imgs/icon.png">
<link rel="apple-touch-icon-precomposed" 
      sizes="114x114" href="imgs/icon.png">
<link rel="apple-touch-icon-precomposed" 
      sizes="72x72" href="imgs/icon.png">
<link rel="apple-touch-icon-precomposed" 
      sizes="57x57" href="imgs/icon.png">
{% endhighlight %}



#### 设置启动页
目前在ios9上还无法显示，具体原因还没找到。如果你知道解决方法，劳烦告诉一下我，多谢！


{% highlight html %}
<!-- 添加启动页 ios9暂无法显示 -->
<!-- iPhone -->
<link href="imgs/splash-320x460.png"
      media="(device-width: 320px) and (device-height: 480px) 
          and (-webkit-device-pixel-ratio: 1)"
      rel="apple-touch-startup-image">
<!-- iPhone (Retina) -->
<link href="imgs/splash-640x920.png"
      media="(device-width: 320px) and (device-height: 480px) 
          and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image">
<!-- iPhone 5 -->
<link href="imgs/splash-640x1096.png"
      media="(device-width: 320px) and (device-height: 568px) 
          and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image">
<!-- iOS 8 iPhone 6 (portrait) -->
<link href="imgs/splash-750x1294.png"
      media="(device-width: 375px) and (device-height: 667px) 
          and (orientation: portrait) 
          and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image">
<!-- iOS 8 iPhone 6 (landscape) -->
<link href="imgs/splash-710x1334.jpg"
      media="(device-width: 375px) and (device-height: 667px) 
          and (orientation: landscape) 
          and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image" >
<!-- iOS 8 iPhone 6 Plus (portrait) -->
<link href="imgs/splash-1242x2148.jpg"
      media="(device-width: 414px) and (device-height: 736px) 
      	  and (orientation: portrait) 
      	  and (-webkit-device-pixel-ratio: 3)"
      rel="apple-touch-startup-image" >
<!-- iOS 8 iPhone 6 Plus (landscape) -->
<link href="imgs/splash-1182x2208.jpg"
      media="(device-width: 414px) and (device-height: 736px) 
          and (orientation: landscape) 
          and (-webkit-device-pixel-ratio: 3)"
      rel="apple-touch-startup-image" >
<!-- iPad (portrait) -->
<link href="apple-touch-startup-image-768x1004.png"
      media="(device-width: 768px) and (device-height: 1024px)
         and (orientation: portrait)
         and (-webkit-device-pixel-ratio: 1)"
      rel="apple-touch-startup-image">
<!-- iPad (landscape) -->
<link href="apple-touch-startup-image-748x1024.png"
      media="(device-width: 768px) and (device-height: 1024px)
         and (orientation: landscape)
         and (-webkit-device-pixel-ratio: 1)"
      rel="apple-touch-startup-image">
<!-- iPad (Retina, portrait) -->
<link href="apple-touch-startup-image-1536x2008.png"
      media="(device-width: 768px) and (device-height: 1024px)
         and (orientation: portrait)
         and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image">
<!-- iPad (Retina, landscape) -->
<link href="apple-touch-startup-image-1496x2048.png"
      media="(device-width: 768px) and (device-height: 1024px)
         and (orientation: landscape)
         and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image">
{% endhighlight %}


#### 弹出添加到主屏幕提示
这里用到了第三方插件[cubiq/add-to-home-screen](http://cubiq.org/add-to-home-screen),支持android、ios。详细用法自己去查看，以下只是在ios上简单的使用。

<img src="{{ '/assets/img/posts/add-to-homescreen-tips.jpg' | prepend: site.baseurl }}" alt=""> 

{% highlight html %}
<!-- 添加提示插件 -->
<link rel="stylesheet" type="text/css" 
    href="addToHomescreen/css/addtohomescreen.css">
<script src="addToHomescreen/js/addtohomescreen.js"></script>

<script>
    <!-- 清除session,每次都弹出提示 -->
    addToHomescreen.removeSession();
    addToHomescreen();
</script>
{% endhighlight %}


#### 最后提供个DEMO以作参考
[查看DEMO项目](https://github.com/howedeng/safari-add-to-homescreen)





