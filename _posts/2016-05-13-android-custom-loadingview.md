---
layout: post
title: Android 自定义一款炫酷的加载控件
permalink: Android-custom-loadingview
date: 2016-05-13 22:13:33
categories: Android
tags: Android
---


**概述：**
----
在设计应用的时候，我们应该热爱极简主义，简单就是好的，对于很多用户来说，复杂的东西并不受欢迎。
我要实现的是根据不同的情况去显示不同的加载效果，随用随调，效果是借鉴于某一项目的效果，我认为有必要提取出来改善封装一下，供以后使用。情况大致分为：加载中、无网络、无数据、加载失败等；
<!-- more -->
#### **预览下效果图**

![这里写图片描述](https://img-blog.csdn.net/20160519152132230)


#### **我们怎么实现这种效果呢**
view_loading.xml的布局如下：

```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:id="@+id/lin_loading"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:visibility="gone"
        android:orientation="vertical">

        <ImageView
            android:id="@+id/img_loading"
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:src="@drawable/loading_animation" />

        <TextView
            android:id="@+id/tv_loading"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:layout_gravity="center_horizontal"
            android:textSize="14sp" />

    </LinearLayout>


    <LinearLayout
        android:id="@+id/lin_load"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:gravity="center"
        android:orientation="vertical"
        android:visibility="visible"

        >

        <ImageView
            android:id="@+id/iv_load"
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:src="@mipmap/ic_launcher" />

        <TextView
            android:id="@+id/tv_load"
            android:layout_width="wrap_content"
            android:layout_gravity="center_horizontal"
            android:layout_height="wrap_content" />


        <Button
            android:id="@+id/btn_load"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:layout_gravity="center_horizontal"
            android:textSize="14sp" />

    </LinearLayout>

</FrameLayout>

```
**从布局来看，我分了两个部分，一个是加载中，另外一个是带有ImagView、文字和按钮的布局，有人看到这，就会说，哇靠，这不是很简单吗？根据不同的情况去设置Visibility的值就好了啊，没错，原理就是这样。**

XHLoadingView.java的代码如下：

```
package com.woyou.loadingdemo.widget;

import android.content.Context;
import android.graphics.drawable.AnimationDrawable;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.Button;
import android.widget.FrameLayout;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;

import com.woyou.loadingdemo.LoadingState;
import com.woyou.loadingdemo.R;


/**
 * Created by Xiho on 11:21.
 */
public class XHLoadingView extends FrameLayout {

    private Context mContext;
    // 加载中的布局
    private LinearLayout mLinearLoad;
    //其他加载的布局
    private LinearLayout mLinearLoading;

    private TextView mTvLoading;

    private TextView mTvLoad;

    private ImageView mIvLoading;

    private ImageView mIvLoad;

    private Button mBtnLoad;

    private LoadingState mState;

    private AnimationDrawable animation;


    public XHLoadingView(Context context) {
        super(context);
        mContext = context;
    }

    public XHLoadingView(Context context, AttributeSet attrs) {
        super(context, attrs);
        mContext = context;
    }

    public XHLoadingView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        mContext = context;
    }

    public XHLoadingView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
        mContext = context;

    }
    public void build(){
        LayoutInflater.from(mContext).inflate(R.layout.view_loading, this, true);

        mLinearLoading = (LinearLayout) findViewById(R.id.lin_loading);

        mLinearLoad = (LinearLayout) findViewById(R.id.lin_load);

        mIvLoading = (ImageView) findViewById(R.id.img_loading);

        mIvLoad = (ImageView) findViewById(R.id.iv_load);

        mTvLoading = (TextView) findViewById(R.id.tv_loading);

        mTvLoad = (TextView) findViewById(R.id.tv_load);

        mBtnLoad = (Button) findViewById(R.id.btn_load);

        mBtnLoad.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                setState(LoadingState.STATE_LOADING);
                mOnRetryListener.onRetry();
            }
        });

    }


    @Override
    public void setVisibility(int visibility) {
        super.setVisibility(visibility);
        if(View.GONE==visibility && mState==LoadingState.STATE_LOADING && animation!=null&&animation.isRunning()){
            animation.stop();
        }
    }

    /**
     * 加载中提示文字
     */
    private String mLoadingText;
    private int mLoadingIcon;
    public XHLoadingView withLoadingIcon(int resId){
        mLoadingIcon = resId;
        return this;
    }

    /**
     * 加载数据为空提示文字
     */
    private String mLoadEmptyText;
    private int mLoadEmptyIcon;
    public XHLoadingView withEmptyIcon(int resId){
        mLoadEmptyIcon = resId;
        return this;
    }

    /**
     * 无网络提示
     */
    private String mLoadNoNetworkText;
    private int mNoNetworkIcon;
    public XHLoadingView withNoNetIcon(int resId){
        mNoNetworkIcon = resId;
        return this;
    }

    private OnRetryListener mOnRetryListener;

    /**
     * 定义重试的的接口
     */
    public interface OnRetryListener {
        void onRetry();
    }

    public XHLoadingView withOnRetryListener(OnRetryListener mOnRetryListener){
        this.mOnRetryListener = mOnRetryListener;
        return this;
    }

    /**
     *  设置加载的状态
     * @param state
     */
    public void setState(LoadingState state){
        if(mState==state){
            return;
        }else if(state==LoadingState.STATE_LOADING){
            mLinearLoading.setVisibility(VISIBLE);
            mLinearLoad.setVisibility(GONE);
        }else if(state!=LoadingState.STATE_LOADING){
            mLinearLoading.setVisibility(GONE);
            mLinearLoad.setVisibility(VISIBLE);
            if(animation!=null && mState==LoadingState.STATE_LOADING)
                animation.stop();
        }
        changeState(state);
    }


    public boolean btnEmptyEnable = true;
    public boolean btnErrorEnable = true;
    public boolean btnNoNetworkEnable = true;

    public XHLoadingView withBtnNoNetEnnable(boolean ennable) {
        btnNoNetworkEnable = ennable;
        return this;
    }

    public XHLoadingView withBtnErrorEnnable(boolean ennable) {
        btnErrorEnable = ennable;
        return this;
    }


    public XHLoadingView withBtnEmptyEnnable(boolean ennable) {
        btnEmptyEnable = ennable;
        return this;
    }

    /**
     * 改变状态
     * @param state
     */
    private void changeState(LoadingState state) {
        switch (state) {
            //加载中
            case STATE_LOADING:
                mState = LoadingState.STATE_LOADING;
                mIvLoading.setImageResource(mLoadingIcon);
                mTvLoading.setText(mLoadingText);
                if (animation == null) {
                    animation = (AnimationDrawable) mIvLoading.getDrawable();
                }
                if (animation != null)
                    animation.start();
                break;
            //数据为空
            case STATE_EMPTY:
                mState = LoadingState.STATE_EMPTY;
                mIvLoad.setImageResource(mLoadEmptyIcon);
                mTvLoad.setText(mLoadEmptyText);
                if (btnEmptyEnable) {
                    mBtnLoad.setVisibility(VISIBLE);
                    mBtnLoad.setText(btn_empty_text);
                } else {
                    mBtnLoad.setVisibility(GONE);
                }
                break;
            //加载失败
            case STATE_ERROR:
                mState = LoadingState.STATE_ERROR;
                mIvLoad.setImageResource(mErrorIco);
                mTvLoad.setText(mLoadErrorText);
                if (btnErrorEnable) {
                    mBtnLoad.setVisibility(VISIBLE);
                    mBtnLoad.setText(btn_error_text);
                } else {
                    mBtnLoad.setVisibility(GONE);
                }
                break;
            //无网络
            case STATE_NO_NET:
                mState = LoadingState.STATE_NO_NET;
                mIvLoad.setImageResource(mNoNetworkIcon);
                mTvLoad.setText(mLoadNoNetworkText);
                if (btnNoNetworkEnable) {
                    mBtnLoad.setVisibility(VISIBLE);
                    mBtnLoad.setText(btn_nonet_text);
                } else {
                    mBtnLoad.setVisibility(GONE);
                }
                break;
        }

    }


    /**
     * 后台或者本地出现错误提示
     */
    private String mLoadErrorText;
    private int mErrorIco;

    public XHLoadingView withErrorIco(int resId) {
        mErrorIco = resId;
        return this;
    }

    /**
     * 加载空数据
     * @param resId
     * @return
     */
    public XHLoadingView withLoadEmptyText(int resId) {
        mLoadEmptyText = getResources().getString(resId);
        return this;
    }

    public XHLoadingView withLoadEmptyText(String mLoadEmptyText) {
        this.mLoadEmptyText = mLoadEmptyText;
        return this;
    }

    /**
     *  无网络时候加载文字
     * @param resId
     * @return
     */
    public XHLoadingView withLoadNoNetworkText(int resId) {
        mLoadNoNetworkText = getResources().getString(resId);
        return this;
    }

    public String btn_empty_text = "重试";
    public String btn_error_text = "重试";
    public String btn_nonet_text = "重试";

    /**
     * 数据为空的Button的文字提示
     * @param text
     * @return
     */
    public XHLoadingView withBtnEmptyText(String text) {
        this.btn_empty_text = text;
        return this;
    }

    /**
     * 加载错误的Button的文字提示
     * @param text
     * @return
     */
    public XHLoadingView withBtnErrorText(String text) {
        this.btn_error_text = text;
        return this;
    }

    /**
     * 加载错误的文字提示
     * @param resId
     * @return
     */
    public XHLoadingView withLoadErrorText(int resId) {
        this.mLoadErrorText = getResources().getString(resId);
        return this;
    }
    public XHLoadingView withLoadErrorText(String mLoadedErrorText) {
        this.mLoadErrorText = mLoadedErrorText;
        return this;
    }

    /**
     * 加载无网络的Button的文字提示
     * @param text
     * @return
     */
    public XHLoadingView withBtnNoNetText(String text) {
        this.btn_nonet_text = text;
        return this;
    }

    /**
     * 加载没有网路的文字提示
     * @param mLoadedNoNetText
     * @return
     */
    public XHLoadingView withLoadNoNetworkText(String mLoadedNoNetText) {
        this.mLoadNoNetworkText = mLoadedNoNetText;
        return this;
    }

    public XHLoadingView withLoadingText(int resId) {
        this.mLoadingText = getResources().getString(resId);
        return this;
    }

    public XHLoadingView withLoadingText(String mLoadingText) {
        this.mLoadingText = mLoadingText;
        return this;
    }

}

```
针对不同的情况作了不同的处理，然后我们在需要的Activity调用。

```
	private XHLoadingView mLoadingView;
	 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_display);
        mLoadingView = (XHLoadingView) findViewById(R.id.lv_loading);
        mLoadingView.withLoadEmptyText("≥﹏≤ , 啥也木有 !").withEmptyIcon(R.drawable.disk_file_no_data).withBtnEmptyEnnable(false)
                    .withErrorIco(R.drawable.ic_chat_empty).withLoadErrorText("(῀( ˙᷄ỏ˙᷅ )῀)ᵒᵐᵍᵎᵎᵎ,我家程序猿跑路了 !").withBtnErrorText("臭狗屎!!!")
                    .withLoadNoNetworkText("你挡着信号啦o(￣ヘ￣o)☞ᗒᗒ 你走").withNoNetIcon(R.drawable.ic_chat_empty).withBtnNoNetText("网弄好了，重试")
                    .withLoadingIcon(R.drawable.loading_animation).withLoadingText("加载中...").withOnRetryListener(new XHLoadingView.OnRetryListener() {
                @Override
                public void onRetry() {
                    SnackbarUtil.show(mLoadingView,"已经在努力重试了",0);
                }
            }).build();
     }

........

  //加载中
      mLoadingView.setVisibility(View.VISIBLE);
      mLoadingView.setState(LoadingState.STATE_LOADING);

 //空数据
	  mLoadingView.setVisibility(View.VISIBLE);
      mLoadingView.setState(LoadingState.STATE_EMPTY)
 //无网络
	  mLoadingView.setVisibility(View.VISIBLE);
      mLoadingView.setState(LoadingState.STATE_NO_NET);

 //加载错误
	  mLoadingView.setVisibility(View.VISIBLE);
      mLoadingView.setState(LoadingState.STATE_ERROR);

.......


   }
```

源码中注释详细，就不用再做过多的解释了吧！

#### **完整代码**：[XHLoadingView](https://github.com/git-xuhao/XHLoadingView)









