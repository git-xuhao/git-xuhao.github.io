---
layout: post
title: Android 网络请求库Retrofit简单使用
permalink: android-retrofit
date: 2016-4-12 16:20:24
categories: Android
tags: Android
desc: Retrofit是一套 RESTful 架构的 Android（Java）客户端实现，基于注解，提供 JSON to POJO（Plain Ordinary Java Object ，简单Java对象），POJO to JSON，网络请求（POST，GET， PUT，DELETE 等）封装。
---


## **什么是 Retrofit ?**
Retrofit是一套 RESTful 架构的 Android（Java）客户端实现，基于注解，提供 JSON to POJO（Plain Ordinary Java Object ，简单Java对象），POJO to JSON，网络请求（POST，GET， PUT，DELETE 等）封装。

既然只是一个网络请求封装库，现在已经有了那么多的大家已经耳熟能详的网络请求封装库了，为什么还要介绍它呢，原因在于 Retrofit 是一套注解形的网络请求封装库，让我们的代码结构更给为清晰。它可以直接解析JSON数据变成JAVA对象，甚至支持回调操作，处理不同的结果。
想更详细的了解 Retrofit，可以查看[官方文档](http://square.github.io/retrofit/) 。

话不多说，直入主题~~
### **一、集成**
目前我使用的是AndroidStudio,那么在model的build.gradle文件中添加以下引用：
```Java
compile 'com.squareup.okhttp3:okhttp:3.2.0' 
compile 'com.squareup.retrofit2:retrofit:2.0.0-beta4'
compile 'com.squareup.retrofit2:converter-gson:2.0.0-beta3'
```
<!-- more -->
**说明：**

Retrofit依赖于okhttp，所以需要集成okhttp
API返回的数据为JSON格式，在此我使用的是Gson对返回数据解析.**请使用最新版的Gson**

### **二、返回的数据格式**
使用的是百度API的数据接口：[名人名言API](http://apistore.baidu.com/apiworks/servicedetail/1756.html)
![这里写图片描述](http://img.blog.csdn.net/20160408133309596)

![这里写图片描述](http://img.blog.csdn.net/20160408133322319)


该接口的API主机地址为：http://apistore.baidu.com；
需要访问的接口：avatardata/mingrenmingyan/lookup；

需要一个key等于apikey的Header和一个keyword等于**名人名言**的查询关键字,而且该请求为**GET**请求.

访问该API返回的数据格式如下：
```Java
{
    "total": 914,
    "result": [
        {
            "famous_name": "布兰登",
            "famous_saying": "人生至善，就是对生活乐观，对工作愉快，对事业兴奋。"
        },
        {
            "famous_name": "魏书生",
            "famous_saying": "抽打自己的鞭子要掌握在自己的手里，在漫长的人生道路的每一步上，都要经常鞭策自警，万不可以为有过一两次抽打就可以沿途平安了。"
        },
        {
            "famous_name": "亨·易卜生",
            "famous_saying": "夺走了普通人生活的幻想，也就等于夺去了他的幸福"
        },
        {
            "famous_name": "佚名",
            "famous_saying": "不知道自己走向何方的人，大都是人生的匆匆过客。"
        },
        {
            "famous_name": "南丁格尔",
            "famous_saying": "人生欲求安全，当有五要。一是清洁空气，二是澄清饮水，三是流通沟渠，四是扫洒屋宇，五是日光充足。"
        }
    ],
    "error_code": 0,
    "reason": "Succes"
}
```

### **三、AndroidStudio插件[ GsonFormat](https://plugins.jetbrains.com/plugin/7654?pr=androidstudio)**

我们根据上面API返回的json数据来创建一个FamousInfo数据对象，我们可以利用AndroidStudio插件[ GsonFormat](https://plugins.jetbrains.com/plugin/7654?pr=androidstudio) 快速，方便的将json数据转为Java 对象，
FamousInfo.java
``` java
package com.woyou.androidsample.bean;

import java.util.List;

/**
 * Created by Xiho on 2016/3/14.
 */
public class FamousInfo {

    /**
     * total : 227
     * result : [{"famous_name":"车尔尼雪夫斯基","famous_saying":"非凡的单纯，非凡的明确\u2014\u2014这是天才的智慧的最可惊人的品质。"},{"famous_name":"约·德莱顿","famous_saying":"天才在社会生活中往往显得迟钝而"},{"famous_name":"雨果","famous_saying":"敢于冲撞命运才是天才"},{"famous_name":"卡莱尔","famous_saying":"所谓天才，就是比任何人都先抵挡痛苦的经验本领。"},{"famous_name":"林肯","famous_saying":"卓越的天才不屑走一条人家走过的路。他寻找迄今没有开拓过的地区。"},{"famous_name":"席勒","famous_saying":"产生天才的土壤比天才还要难找"},{"famous_name":"爱因斯坦","famous_saying":"任何天才不能在孤独的状态中发展"},{"famous_name":"民谚","famous_saying":"名人的古怪行为是天才的标志，凡人的古怪行为是神经出了毛病"},{"famous_name":"鲁迅","famous_saying":"哪里有天才，我是把别人喝咖啡的工夫都用在了工作上了。"},{"famous_name":"塞涅夫","famous_saying":"没有某些发狂的劲头，就没有天才。"},{"famous_name":"狄德罗","famous_saying":"精神的浩瀚想象的活跃心灵的勤奋：就是天才。"},{"famous_name":"爱默生","famous_saying":"平凡的人希望，天才的人创造。"},{"famous_name":"契诃夫","famous_saying":"真正的天才是常常隐藏在群众里面，绝不挤向人前去露脸的。"},{"famous_name":"别林斯基","famous_saying":"任何天才，不经过艰苦不断的劳动，不经过最使空想家头疼和懊恼的最初纯物质和机械的劳动，就无法精通任何种类的艺术。"},{"famous_name":"杨格","famous_saying":"我愿意以天才比美德，以学问比财富。如美德越少的人，越需要财富，天才越低的人，越需要学问。"},{"famous_name":"巴尔扎克","famous_saying":"职业尽管不同，但天才的品德并无分别。"},{"famous_name":"恩格斯","famous_saying":"逆境使天才脱颖而出，顺境会埋没"},{"famous_name":"巴尔扎克","famous_saying":"破坏的人和建设的人，两者都是意志的现象：一个是准备工作，另一个是完成工作；前者好像是一个恶的天才，后者似乎是一个善的天才；对这一个给予光荣，对另一个给予忘却。恶者哇啦哇啦，把庸俗的人们从梦里惊醒，对他佩服得五体投地，可是善者却一直默不作声。"},{"famous_name":"培根","famous_saying":"如果孩子确有某种超群的天才，那当然应该扶植发展。但就一般情况说，下面这句格言很有用的：\u201c长期的训练会通过适应化难为易。\u201d"},{"famous_name":"爱迪生","famous_saying":"天才是百分之一的灵感，百分之九十九的血汗。"}]
     * error_code : 0
     * reason : Succes
     */

    private int total;
    private int error_code;
    private String reason;
    /**
     * famous_name : 车尔尼雪夫斯基
     * famous_saying : 非凡的单纯，非凡的明确——这是天才的智慧的最可惊人的品质。
     */

    private List<ResultEntity> result;

    public void setTotal(int total) {
        this.total = total;
    }

    public void setError_code(int error_code) {
        this.error_code = error_code;
    }

    public void setReason(String reason) {
        this.reason = reason;
    }

    public void setResult(List<ResultEntity> result) {
        this.result = result;
    }

    public int getTotal() {
        return total;
    }

    public int getError_code() {
        return error_code;
    }

    public String getReason() {
        return reason;
    }

    public List<ResultEntity> getResult() {
        return result;
    }

    public static class ResultEntity {
        private String famous_name;
        private String famous_saying;

        public void setFamous_name(String famous_name) {
            this.famous_name = famous_name;
        }

        public void setFamous_saying(String famous_saying) {
            this.famous_saying = famous_saying;
        }

        public String getFamous_name() {
            return famous_name;
        }

        public String getFamous_saying() {
            return famous_saying;
        }
    }
}


```

### **四、实现过程**
首先， 按照官方的说明，我们需要创建一个接口，返回 Call<FamousInfo>；如下：
```java
@GET("/avatardata/mingrenmingyan/lookup")
    Call<FamousInfo> getFamousResult(@Header("apiKey") String apiKey,
                                     @Query("keyword") String keyword,
                                     @Query("page") int page,
                                     @Query("rows") int rows);

```
这里我们使用的是Retrofit 提供注解的方式来定义接口的
* @get后面我们填写需要访问对应的接口地址
* @Header用来添加Header
* @Query用来添加查询关键字

现在接口定义好了，我们来** 定义Retrofit 网络接口服务的包装类 **：

```java
package com.woyou.androidsample;

import android.content.Context;

import retrofit2.GsonConverterFactory;
import retrofit2.Retrofit;

/**
 * Retrofit 网络接口服务的包装类
 * Created by Xiho on 2016/3/14.
 */
public class RetrofitWrapper {
    private static RetrofitWrapper instance;
    private Context mContext;
    private Retrofit retrofit;

    private RetrofitWrapper() {
           //1.创建Retrofit对象
        retrofit = new Retrofit.Builder().baseUrl(Constant.BASEURL) // 定义访问的主机地址
                .addConverterFactory(GsonConverterFactory.create())  //解析方法
                .build();
    }
    /**
     * 单例模式
     *
     * @return
     */
    public static RetrofitWrapper getInstance() {
        if (instance == null) {
            synchronized (RetrofitWrapper.class){
                if (instance==null){
                    instance = new RetrofitWrapper();
                }
            }
        }
        return instance;
    }


    public <T> T create(final Class<T> service) {
        return retrofit.create(service);
    }

}



/** 
   * Created by Xiho on 2016/3/14. 
   */
public class Constant {    
    public  static String BASEURL="http://apis.baidu.com"; //服务器地址   
    public  static String APIKEY="4c4f0c3c49e09d5578ae0ba49fa84a97";

}
```

网络服务的包装类定义好了之后，在定义一个访问的Model（个人编码风格，其实可以更简洁点）。

``` java 
public class FamousInfoModel {
    private static FamousInfoModel famousInfoModel;
    private FamousService mFamousService;

    /**
     * 单例模式
     *
     * @return
     */
    public static FamousInfoModel getInstance(Context context) {
        if (famousInfoModel == null) {
            famousInfoModel = new FamousInfoModel(context);
        }
        return famousInfoModel;
    }


    private FamousInfoModel(Context context) {
        mFamousService = (FamousService) RetrofitWrapper.getInstance().create(FamousService.class);
    }

    /**
     * 查询名人名言
     *
     * @param famousInfoReq
     * @return
     */
    public Call<FamousInfo> queryLookUp(FamousInfoReq famousInfoReq) {
        Call<FamousInfo> infoCall = mFamousService.getFamousResult(famousInfoReq.apiKey, famousInfoReq.keyword, famousInfoReq.page, famousInfoReq.rows);
        return infoCall;
    }
}

```

### **五、如何使用** 
构建好接口以后,可以使用了!

使用分为四步:

* 创建Retrofit对象
* 创建访问API的请求
* 发送请求
* 处理结果
主要代码如下：
```java
FamousInfoModel famousInfoModel =FamousInfoModel.getInstance(getApplicationContext());
// 获取事件
    private void initEvent() {
        mSerachBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //创建访问的API请求
                Call<FamousInfo> callFamous= famousInfoModel.queryLookUp(initParams());
                //发送请求
                callFamous.enqueue(new Callback<FamousInfo>() {
                    @Override
                    public void onResponse(Call<FamousInfo> call, Response<FamousInfo> response) {
                        if(response.isSuccess()){
                            FamousInfo result = response.body();
                            if(result!=null){
                                List<FamousInfo.ResultEntity> entity = result.getResult();
                                    mTxtContent.setText("1、"+entity.get(0).getFamous_saying()+"\n---"+entity.get(0).getFamous_name()+"\n 2、"
                                            +entity.get(1).getFamous_saying()+"\n---"+entity.get(1).getFamous_name());
                            }
                        }
                    }
                    @Override
                    public void onFailure(Call<FamousInfo> call, Throwable t) {

                    }
                });
            }
        });
    }
```
最后运行的效果图如下：

![这里写图片描述](http://img.blog.csdn.net/20160408133246502)

** 搜索的结果我只是显示了其中一部分，只用来使用Retrofit 这个框架，没有很具体去做一些处理啦，后面还会用一些其他的库，还会使用本Demo 来进行测试，所以这次就简单写了下。**   <br />
附上源码：[AndroidRetrofitSample](http://download.csdn.net/download/u011974987/9461917)   <br />

### **六、扩展阅读**

** Retrofit:** 
* Retrofit 官方文档：[http://square.github.io/retrofit/](http://square.github.io/retrofit/)
* Retrofit 使用介绍：[http://www.cnblogs.com/angeldevil/p/3757335.html](http://www.cnblogs.com/angeldevil/p/3757335.html)
* Retrofit 离线缓存策略：[http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0115/3873.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0115/3873.html)

> 转载请标明出处： http://blog.csdn.net/u011974987/article/details/50895633