---
layout: post
title:  "获取优酷视频M3u8文件"
date:   2015-01-25
image: password.jpg
description: 获取优酷真实视频地址，android/ios通用
categories: [android,apple]
tags: [android,ios, m3u8, youku, WebView, video]

---

## 前言
网上有很多获取优酷真实视频地址的方法，不是失效了，就是很快就被封了，以下方法模拟youku网页方式获取真实地址，十分稳定(2016/12改)

## 获取优酷视频链接或者ID
e.g

`http://v.youku.com/v_show/id_XNjQyNjE3MjY4.html`

XNjQyNjE3MjY4 就是相应地Id,至于怎样获取看你们自己了

## 获取M3U8文件
创建后台WebView来跟踪上面的链接以获取M3U8文件

设置WebSettings

{% highlight java %}
WebSettings settings = this.getSettings();
 
// 支持javascript
settings.setJavaScriptEnabled(true);
 
// 支持localstorage和essionStorage
settings.setDomStorageEnabled(true);
 
// 开启应用缓存
settings.setAppCacheEnabled(true);
String cacheDir = context.getApplicationContext()
        .getDir("cache", Context.MODE_PRIVATE).getPath();
settings.setAppCachePath(cacheDir);
settings.setCacheMode(WebSettings.LOAD_DEFAULT);
 
// 设置缓冲大小
settings.setAppCacheMaxSize(1024 * 1024 * 10);
 
settings.setAllowFileAccess(true);
 
settings.setUserAgentString(
        "Mozilla/5.0 (iPhone; CPU iPhone OS 7_1 like Mac OS X) AppleWebKit/537.51.2 (KHTML, like Gecko) Version/7.0 Mobile/11D5145e Safari/9537.53");

{% endhighlight %}

设置WebViewClient
{% highlight java %}
this.setWebViewClient(new WebViewClient() {
 
    @Override
    public void onPageFinished(WebView view, String url) {
 
        view.loadUrl("javascript:window.js_method.showSource"+
                            "(document.getElementsByTagName('video')[0].src);");
        super.onPageFinished(view, url);
    }
 
});
{% endhighlight %}

添加addJavascriptInterface
{% highlight java %}
this.addJavascriptInterface(new InJavaScriptLocalObj(), "js_method");
 
final class InJavaScriptLocalObj {
    @JavascriptInterface
    public void showSource(String html5url) {
        if (html5url != null) {
            if (html5url.contains("type=flv")) {
                html5url = html5url.replace("type=flv", "type=mp4");
            } else if (html5url.contains("type=mp4")) {
            } else {
                html5url = html5url + "&type=mp4";
            }
            Message msg = new Message();
            msg.obj = html5url;//这个就是M3U8文件地址
            handler.sendMessage(msg);
        }
 
    }
}
{% endhighlight %}

剩下的就是读取m3u8文件并解析成一个视频地址列表，按次序播放，该方法ios通用

[DEMO](https://pan.baidu.com/share/init?shareid=88167913&uk=3040070900)(k8pr)
