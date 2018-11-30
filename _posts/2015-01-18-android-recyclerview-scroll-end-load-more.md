---
layout: post
title:  "Android RecyclerView 滑动到最后加载更多"
date:   2015-01-18
image: password.jpg
description: RecyclerVIew 滑动到底部加载更多数据实现
category: android
tags: [android, RecyclerView, loadmore, ScrollView]

---

很简单，监听一下OnScrollListener，以横向滑动为例

{% highlight java %}
mRecycleView.setOnScrollListener(new RecyclerView.OnScrollListener(){
    //用来标记是否正在向最后一个滑动，既是否向右滑动或向下滑动
    boolean isSlidingToLast = false;
 
    @Override
    public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
        LinearLayoutManager manager = (LinearLayoutManager)recyclerView.getLayoutManager();
        // 当不滚动时
        if (newState == RecyclerView.SCROLL_STATE_IDLE) {
            //获取最后一个完全显示的ItemPosition
            int lastVisibleItem = manager.findLastCompletelyVisibleItemPosition();
            int totalItemCount = manager.getItemCount();
 
            // 判断是否滚动到底部，并且是向右滚动
            if (lastVisibleItem == (totalItemCount -1) && isSlidingToLast) {
                //加载更多功能的代码
                Ln.e("howes right="+manager.findLastCompletelyVisibleItemPosition());
                Toast.makeText(getActivityContext(),"加载更多",0).show();
            }
        }
    }
 
    @Override
    public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
        super.onScrolled(recyclerView, dx, dy);
        //dx用来判断横向滑动方向，dy用来判断纵向滑动方向
        if(dx > 0){
            //大于0表示，正在向右滚动
            isSlidingToLast = true;
        }else{
            //小于等于0 表示停止或向左滚动
            isSlidingToLast = false;
        }
 
    }
});
{% endhighlight %}

