---
layout: post
title: Android Provision setupwizard 向导程序
key: 20160112
tags: Provision setupwizard
category: Android
date: 2016-01-12 11:14:12 +08:00
desc: 先来说说我为什么写下这篇文章，最近再在一个App，就相当于Android系统中 Provison.apk 的作用，可能很多朋友都不知道有这个APK 的存在。它的主要作用是作为开机引导用户进行一些基本设置。
---
   先来说说我为什么写下这篇文章，最近再在一个App，就相当于Android系统中 Provison.apk 的作用，可能很多朋友都不知道有这个APK 的存在。它的主要作用是作为开机引导用户进行一些基本设置。但是在原生的 android 系统中，这个 provision 非常的简单，只有一个空白的 activity，这个主要就是留给 厂商自己定制的（像 google 的 nexus 进行让里你登陆 google 帐号，连接WiFi等等一些功能）。
    
   Android 原生的Provision 只做了一件事，就是写入一个DEVICE_PROVISIONED标记。不过这个标记作用很大，这个标记只会在系统全新升级（双清）的时候写入一次，代表了Android系统升级准备完成，可以正常工作。
之所以会关注DEVICE_PROVISIONED这个标记，是因为当用户操作完的App 的时候我设置了这个标记，等其他逻辑，导致外部程序进入的时候黑屏，为了排查原因，所以就从这入手了。于是，查资料，Google等。我项目里面也设置了一个属性比 Lanuch桌面启动还早，看下`AndroidManifest.xml`:

```
<application>  
    <activity android:name="DefaultActivity"  
            android:excludeFromRecents="true">  
        <intent-filter android:priority="1">  
            <action android:name="android.intent.action.MAIN" />  
            <category android:name="android.intent.category.HOME" />  
            <category android:name="android.intent.category.DEFAULT" />  
        </intent-filter>  
    </activity>  
</application>
```

Provision 是 android 中的一个系统应用（源码位置在： packages/apps/Provision 下面）。
其实这个就是一个桌面而已，为什么比普通桌面启动快，只不过它把优先级设置得比普通桌面高了一点而已： android:priority=”1” ，普通桌面不设置的话，默认是 0。package manager 在解析 Intent 的时候会优先返回优先级高的。
如果两个优先级一样的话，你启动时候他会弹出一个对话框让你选择一个程序来执行。
从上面代码可以看到两个比较有意思的地方：

- 第一：Provision配置了category.HOME属性，做系统开发的朋友应该都了解，category.HOME是桌面程序的标记，简单点说就是Launcher程序才会配置的标记。
- 第二：priority=1，配置了优先级，也就是说它的优先级比我们原生的Launcher优先级还要高，它会在Launcher启动前就运行起来。
  然后这个 apk 的就只有一个 activity：

```
public class DefaultActivity extends Activity {  
    @Override  
    protected void onCreate(Bundle icicle) {  
        super.onCreate(icicle);  
        // Add a persistent setting to allow other apps to know the device has been provisioned.  
        Settings.Global.putInt(getContentResolver(), Settings.Global.DEVICE_PROVISIONED, 1);   
        Settings.Secure.putInt(getContentResolver(), Settings.Secure.USER_SETUP_COMPLETE, 1);   
        // remove this activity from the package manager.  
        PackageManager pm = getPackageManager();  
        ComponentName name = new ComponentName(this, DefaultActivity.class);  
        pm.setComponentEnabledSetting(name, PackageManager.COMPONENT_ENABLED_STATE_DISABLED,  
                PackageManager.DONT_KILL_APP);  
        // terminate the activity.  
        finish();  
    }  
}
```

上面就是Provision的全部源码，Provision只有一个Activity，而且做的事情不多。主要做了两件事：

- 设置DEVICE_PROVISIONED标记
- 禁止Provision自己的Activity组件

整个Provision程序只做了这两件事，DEVICE_PROVISIONED就是我们上面说的系统升级完标记。
禁止组件：
	上面说了Provision其中一个功能，写入一个标记。除了写入标记，还有一个禁止本身Activity组件的功能。先简单介绍一下禁止组件的API。
`void setComponentEnabledSetting (ComponentName componentName, int newState, int flags)`
	componentName：组件名称 
	newState：组件新的状态，可以设置三个值，分别是如下： 

- 不可用状态：COMPONENT_ENABLED_STATE_DISABLED 
- 可用状态：COMPONENT_ENABLED_STATE_ENABLED 
- 默认状态：COMPONENT_ENABLED_STATE_DEFAULT 

上面就是记录了package里面被禁止的组件信息。当然，package.xml里面还包含了应用包所有信息，这个可以自己看一下。

　　这个组件只会运行一次，所以我们如果没有格式化/data目录，这个组件就会被禁止。所以会导致一个问题，就是我们的DEVICE_PROVISIONED标记不会再次写入。
        这条记录非常重要，代表设备已经准备就绪，可以正常使用，换句话说，如果没有这条记录的话，那设备是无法正常使用的。事实上还真是这样，因为系统中那一票 services 都回检测这个值，如果没有的话，都回相应的罢工，例如说无法锁屏、按 Home 键无反应、无法发通知等等各种坑爹问题~~
	好了，大概就是这么多~