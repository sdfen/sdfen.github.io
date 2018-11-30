---
layout: post
title:  "Android 使用RecyclerView实现横向滑动"
date:   2015-01-17
image: password.jpg
description: 不错的横向滚动实现方法
category: android
tags: [android, RecyclerView, GalleryView, ScrollView]

---

## 前言
有个需求，实现类似GalleryView左右滑动功能，刚开始打算用HorizontalScrollView嵌套GridView实现，但发现这弊端太多了,Adapter功能基本废了…果断抛弃。

至于为什么不用GalleryView实现，呵呵，谁用谁知道！

后来搜了一下发现Google 在Android L中加入了RecyclerView,可通过引入support-v7包在低版本中使用，下面讲一下初次体验。

## 引入Support-v7.jar包
Gradle 导入
{% highlight xml %}
dependencies {
    //省略其他引入....
 
    //在build.gradle加上这一句就可以，很方便有木有
    compile 'com.android.support:recyclerview-v7:+'
 
}
{% endhighlight %}
至于其他环境，自己到sdk里找jar包吧…懒…

## xml布局
{% highlight xml %}
<android.support.v7.widget.RecyclerView
        xmlns:tools="http://schemas.android.com/tools"
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
{% endhighlight %}

## Activity代码

{% highlight java %}
//必须设置LinearlayoutManager,不然无法用
LinearLayoutManager manager = new LinearLayoutManager(getActivity());
 
//设置水平方向滑动，在xml设置不生效
manager.setOrientation(LinearLayoutManager.HORIZONTAL);
 
recycleView.setLayoutManager(manager);
 
//设置适配器，跟ListView和gridView一样
recycleView.setAdapter(adapter);
{% endhighlight %}


## Adapter代码
跟ListView 和 GridView 的BaseAdapter有所不同，RecyclerView.Adapter直接复用的是Holder,而并非View.

{% highlight java %}
public class PersonAdapter extends 
    RecyclerView.Adapter<PersonAdapter.Holder>{  
 
    private LayoutInflater mInflater;  
    private List<Person> mDatas;  
 
    public GalleryAdapter(List<Person> datats){  
        mDatas = datats;  
    }  
 
    //继承RecyclerView.ViewHolder
    public static class Holder extends RecyclerView.ViewHolder{  
 
        TextView name;  
        TextView age;
 
        public ViewHolder(View view){  
            super(view);  
            //初始化item
            name = (TextView)view.findViewById(R.id.xxxx);
            age = (TextView)view.findViewById(R.id.xxxx);
        }          
    }  
 
    //获取总条目，相当于BaseAdapter里getCount
    @Override 
    public int getItemCount(){ 
        return mDatas.size();  
    }  
 
    /** 
     * 创建ViewHolder 
     */ 
    @Override 
    public ViewHolder onCreateViewHolder(ViewGroup viewGroup, int i){  
        View view = View.inflate(viewGroup.getContext(),R.layout.xx,  
            null);  
 
        Holder holder = new Holder(view);  
 
        return holder;  
    }  
 
    /** 
     * 设置值 ,相当于getView()
     */ 
    @Override 
    public void onBindViewHolder(final ViewHolder holder, final int i){  
 
        holder.name.setText(mDatas.get(i).getName());  
        holder.age.setText(mDatas.get(i).getAge()); 
    }  
 
}  
{% endhighlight %}

## 遇到的问题
NullPointException
{% highlight java %}
java.lang.NullPointerException
    at android.support.v7.widget.RecyclerView.onMeasure(RecyclerView.java:1694)
    at android.view.View.measure(View.java:16497)
    at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:5125)
    ...
{% endhighlight %}
出现这异常，主要因为一开始我没给他配置LinearLayoutManager,解决方法：

{% highlight java %}
//可以看上面的Activity代码
 
mLayoutManager = new LinearLayoutManager(this);
recycleView.setLayoutManager(mLayoutManager);
{% endhighlight %}

Recycleview notifyDataSetChanged() IllegalStateException
{% highlight java %}
java.lang.IllegalStateException: Cannot call this method while RecyclerView is computing a layout or scrolling
        at android.support.v7.widget.RecyclerView.assertNotInLayoutOrScroll(RecyclerView.java:1462)
        at android.support.v7.widget.RecyclerView$RecyclerViewDataObserver.onChanged(RecyclerView.java:2982)
        at android.support.v7.widget.RecyclerView$AdapterDataObservable.notifyChanged(RecyclerView.java:7493)
{% endhighlight %}

Stackoverflow上解决方法
[猛击我，懒得贴代码](http://stackoverflow.com/questions/27070220/recycleview-notifydatasetchanged-illegalstateexception)

## 结语

大体上跟ListView，GridView使用差不多，再也不用担心横向滑动了
