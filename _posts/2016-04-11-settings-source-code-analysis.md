---
layout: post
title: Android 5.1 Settings源码简要分析
permalink: android-5.1-settings
date: 2016-4-11 17:05:24
categories: Android
tags: Android
---

## **概述：** ##
**先声明：**本人工作快两年了，仍是菜鸟级别的，惭愧啊！以前遇到好多知识点都没有记录下来，感觉挺可惜的，现在有机会接触Android 源码。我们一个Android组的搞Setting，我觉得是得写得东西，毕竟才接触，现在只能看一段时间代码，就先记录下一些收获吧，说多了就是泪~本文主要针对L平台上Settings模块正常启动流程做一个简要分析，并试着分析一下Settings下面某选项的实现。

### **Setting 简介**
在之前的KK平台上Settings模块的第一个Activity名字为Settings，其继承的是PreferenceActivity，设置的每一个选项都是对应的一个Header对象，并且Header对象允许显示switch控件，button控件，checkbox控件等。如下图1.1，WLAN和蓝牙上使用到了switch开关。但在L上面，WLAN和蓝牙的这两个开关已经去掉了，如图1.2，在Settings模块的首个页面似乎就只是一个普通的Listview，那它用的还是不是Header呢？或者说取而代之的是什么呢？继续往下看吧~

![图片1.1](https://img-blog.csdn.net/20160329120918137)  
**图片-1.1**
<!-- more -->
![这里写图片描述](https://img-blog.csdn.net/20160329121723202)
**图片-1.2**
### **L Settings 模块首界面初始化流程**

L Settings模块首界面为Settings，继承自SettingsActivity，SettingsActivity继承自Activity。

首先看一下Settings.java代码可以发现它没有重写任何SettingsActiviy的方法，也没有增加任何自己的方法，唯独增加了许多静态内部类，如：

```
/**
 * Top-level Settings activity
 */
public class Settings extends SettingsActivity {

    /*
    * Settings subclasses for launching independently.
    */
    public static class BluetoothSettingsActivity extends SettingsActivity { /* empty */ }
    public static class WirelessSettingsActivity extends SettingsActivity { /* empty */ }
    public static class SimSettingsActivity extends SettingsActivity { /* empty */ }
    public static class TetherSettingsActivity extends SettingsActivity { /* empty */ }
    public static class VpnSettingsActivity extends SettingsActivity { /* empty */ }
    public static class DateTimeSettingsActivity extends SettingsActivity { /* empty */ }
    public static class StorageSettingsActivity extends SettingsActivity { /* empty */ }
    public static class WifiSettingsActivity extends SettingsActivity { /* empty */ }
    public static class WifiP2pSettingsActivity extends SettingsActivity { /* empty */ }
    public static class InputMethodAndLanguageSettingsActivity extends SettingsActivity { /* empty */ }
    public static class KeyboardLayoutPickerActivity extends SettingsActivity { /* empty */ }
    public static class InputMethodAndSubtypeEnablerActivity extends SettingsActivity { /* empty */ }
    public static class VoiceInputSettingsActivity extends SettingsActivity { /* empty */ }
    public static class SpellCheckersSettingsActivity extends SettingsActivity { /* empty */ }
    public static class LocalePickerActivity extends SettingsActivity { /* empty */ }
    public static class UserDictionarySettingsActivity extends SettingsActivity { /* empty */ }
    public static class HomeSettingsActivity extends SettingsActivity { /* empty */ }
    ...
    }
```

看注释可以知道，这些子类是为了启动特定独立的Settings选项而创建的，例如在某个应用里需要设置无线那么只需要启动 WirelessSettingsActivity 就可以了。

 

所以Settings模块的启动流程直接看SettingsActiviy就行了。
1. **SettingsActivity.onCreate方法**
 onCreate方法是Activity的生命周期第一步，看看 SettingsActivity在这里都做了些什么？
 

```
 // Should happen before any call to getIntent()

     getMetaData();
```

这个方法用来获得Activity的额外数据mFragmentClass，如果可以获得这个数据，那么下面会去显示mFragmentClass对应的Activity。直接启动Settings模块不会获得这个数据。

```
     mIsShowingDashboard = className.equals(Settings.class.getName());
```
这一步很重要，因为我们是从Settings这个Activity过来的，所以这里的 mIsShowingDashboard 为 true 。

```
		 // This is a "Sub Settings" when:

        // - this is a real SubSettings

        // - or :settings:show_fragment_as_subsetting is passed to the Intent

        final boolean isSubSettings = className.equals(SubSettings.class.getName()) ||

                intent.getBooleanExtra(EXTRA_SHOW_FRAGMENT_AS_SUBSETTING, false);
```
这个判断很重要但很明显这时isSubSettings的值是fasle，暂时忽略。

```
  setContentView(mIsShowingDashboard ?

                R.layout.settings_main_dashboard : R.layout.settings_main_prefs);
```
前面知道这里的 mIsShowingDashboard为true，所以这里使用的布局文件为R.layout.settings_main_dashboard。settings_main_dashboard.xml文件如下：

 

```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"

             android:id="@+id/main_content"

             android:layout_height="match_parent"

             android:layout_width="match_parent"

             android:background="@color/dashboard_background_color"

             />
```

由于mIsShowingDashboard为true，直接走到下面这段

```
  else {

                // No UP affordance if we are displaying the main Dashboard

                mDisplayHomeAsUpEnabled = false;

                // Show Search affordance

                mDisplaySearch = true;

                mInitialTitleResId = R.string.dashboard_title;

                switchToFragment(DashboardSummary.class.getName(), null, false, false,

                        mInitialTitleResId, mInitialTitle, false);

              }


```
这里看到switchToFragment这个方法，可以知道这里是要切换DashboardSummary这个Fragment.

 

接下来就看看DashboardSummary是个什么玩意？

 

dashboard中文意思是仪表盘，这里是指DashboardSummary就是用来显示Settings所有选项的。

在DashboardSummary的onCreateView里加载了这个布局文件R.layout.dashboard

```
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"

    android:id="@+id/dashboard"

    android:layout_width="match_parent"

    android:layout_height="match_parent"

    android:scrollbarStyle="outsideOverlay"

    android:clipToPadding="false">

 

        <LinearLayout

                android:id="@+id/dashboard_container"

                android:layout_width="match_parent"

                android:layout_height="match_parent"

                android:layout_gravity="center_horizontal"

                android:paddingStart="@dimen/dashboard_padding_start"

                android:paddingEnd="@dimen/dashboard_padding_end"

                android:paddingTop="@dimen/dashboard_padding_top"

                android:paddingBottom="@dimen/dashboard_padding_bottom"

                android:orientation="vertical"

                />

 

</ScrollView>
```
看了上面的布局文件可以知道Settings的选项视图应该就是显示在dashboard_container中了。

  <br/>
DashboardSummary走完onCreateView方法后会走onResume,然后一路下来又会调到SettingsActivity的

```
loadCategoriesFromResource(R.xml.dashboard_categories, categories);
```

这一步是通过 R.xml.dashboard_categories来加载categories，这里的categorys为ArrayList<DashboardCategory> mCategories。接着来看看dashboard_categories.xml这个文件吧

```
<?xml version="1.0" encoding="utf-8"?>
<!-- Copyright (C) 2014 The Android Open Source Project

     Licensed under the Apache License, Version 2.0 (the "License");
     you may not use this file except in compliance with the License.
     You may obtain a copy of the License at

          http://www.apache.org/licenses/LICENSE-2.0

     Unless required by applicable law or agreed to in writing, software
     distributed under the License is distributed on an "AS IS" BASIS,
     WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     See the License for the specific language governing permissions and
     limitations under the License.
-->

<dashboard-categories
        xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- WIRELESS and NETWORKS -->
    <dashboard-category
            android:id="@+id/wireless_section"
            android:title="@string/header_category_wireless_networks" >

        <!-- Wifi -->
        <dashboard-tile
                android:id="@+id/wifi_settings"
                android:title="@string/wifi_settings_title"
                android:fragment="com.android.settings.wifi.WifiSettings"
                android:icon="@drawable/ic_settings_wireless"
                />

        <!--HetComm-->
        <dashboard-tile
                android:id="@+id/hetcomm_settings"
                android:icon="@drawable/ic_settings_hetcomm"
                android:title="@string/hetcom_setting_title">
            <intent android:action="com.android.settings.HETCOMM_SETTINGS" />
        </dashboard-tile>

        <!-- Bluetooth -->
        <dashboard-tile
                android:id="@+id/bluetooth_settings"
                android:title="@string/bluetooth_settings_title"
                android:fragment="com.android.settings.bluetooth.BluetoothSettings"
                android:icon="@drawable/ic_settings_bluetooth2"
                />
                
        <!-- Hotknot -->
        <dashboard-tile
                android:id="@+id/hotknot_settings"
                android:title="@string/hotknot_settings_title"
                android:fragment="com.mediatek.settings.hotknot.HotKnotSettings"
                android:icon="@drawable/ic_settings_hotknot" 
                />

        <!-- SIM Cards -->
        <dashboard-tile
                android:id="@+id/sim_settings"
                android:title="@string/sim_settings_title"
                android:fragment="com.android.settings.sim.SimSettings"
                android:icon="@drawable/ic_sim_sd"
                />

        <!-- Data Usage -->
        <dashboard-tile
                android:id="@+id/data_usage_settings"
                android:title="@string/data_usage_summary_title"
                android:fragment="com.android.settings.DataUsageSummary"
                android:icon="@drawable/ic_settings_data_usage"
                />

        <!-- Operator hook -->
        <dashboard-tile
                android:id="@+id/operator_settings"
                android:fragment="com.android.settings.WirelessSettings" >
            <intent android:action="com.android.settings.OPERATOR_APPLICATION_SETTING" />
        </dashboard-tile>

        <!-- Other wireless and network controls -->
        <dashboard-tile
                android:id="@+id/wireless_settings"
                android:title="@string/radio_controls_title"
                android:fragment="com.android.settings.WirelessSettings"
                android:icon="@drawable/ic_settings_more"
                />

    </dashboard-category>

    <!-- DEVICE -->
    <dashboard-category
            android:id="@+id/device_section"
            android:title="@string/header_category_device" >

        <!-- Home -->
        <dashboard-tile
                android:id="@+id/home_settings"
                android:title="@string/home_settings"
                android:fragment="com.android.settings.HomeSettings"
                android:icon="@drawable/ic_settings_home"
                />

        <!-- Display -->
        <dashboard-tile
                android:id="@+id/display_settings"
                android:title="@string/display_settings"
                android:fragment="com.android.settings.DisplaySettings"
                android:icon="@drawable/ic_settings_display"
                />

        <!-- Notifications -->
        <dashboard-tile
                android:id="@+id/notification_settings"
                android:title="@string/notification_settings"
                android:fragment="com.mediatek.audioprofile.AudioProfileSettings"
                android:icon="@drawable/ic_settings_notifications"
                />

        <!-- Storage -->
        <dashboard-tile
                android:id="@+id/storage_settings"
                android:title="@string/storage_settings"
                android:fragment="com.android.settings.deviceinfo.Memory"
                android:icon="@drawable/ic_settings_storage"
                />

        <!-- Battery -->
        <dashboard-tile
                android:id="@+id/battery_settings"
                android:title="@string/power_usage_summary_title"
                android:fragment="com.android.settings.fuelgauge.PowerUsageSummary"
                android:icon="@drawable/ic_settings_battery"
                />

        <!-- Application Settings -->
        <dashboard-tile
                android:id="@+id/application_settings"
                android:title="@string/applications_settings"
                android:fragment="com.android.settings.applications.ManageApplications"
                android:icon="@drawable/ic_settings_applications"
                />

        <!-- Manage users -->
        <dashboard-tile
                android:id="@+id/user_settings"
                android:title="@string/user_settings_title"
                android:fragment="com.android.settings.users.UserSettings"
                android:icon="@drawable/ic_settings_multiuser"
                />

        <!-- Manage NFC payment apps -->
        <dashboard-tile
                android:id="@+id/nfc_payment_settings"
                android:title="@string/nfc_payment_settings_title"
                android:fragment="com.android.settings.nfc.PaymentSettings"
                android:icon="@drawable/ic_settings_nfc_payment"
                />

        <!-- Manufacturer hook -->
        <dashboard-tile
                android:id="@+id/manufacturer_settings"
                android:fragment="com.android.settings.WirelessSettings">
            <intent android:action="com.android.settings.MANUFACTURER_APPLICATION_SETTING" />
        </dashboard-tile>

    </dashboard-category>

    <!-- PERSONAL -->
    <dashboard-category
            android:id="@+id/personal_section"
            android:title="@string/header_category_personal" >

        <!-- Location -->
        <dashboard-tile
                android:id="@+id/location_settings"
                android:title="@string/location_settings_title"
                android:fragment="com.android.settings.location.LocationSettings"
                android:icon="@drawable/ic_settings_location"
                />

        <!-- Security -->
        <dashboard-tile
                android:id="@+id/security_settings"
                android:title="@string/security_settings_title"
                android:fragment="com.android.settings.SecuritySettings"
                android:icon="@drawable/ic_settings_security"
                />

        <!-- Account -->
        <dashboard-tile
                android:id="@+id/account_settings"
                android:title="@string/account_settings_title"
                android:fragment="com.android.settings.accounts.AccountSettings"
                android:icon="@drawable/ic_settings_accounts"
                />

        <!-- Language -->
        <dashboard-tile
                android:id="@+id/language_settings"
                android:title="@string/language_settings"
                android:fragment="com.android.settings.inputmethod.InputMethodAndLanguageSettings"
                android:icon="@drawable/ic_settings_language"
                />

        <!-- Backup and reset -->
        <dashboard-tile
                android:id="@+id/privacy_settings"
                android:title="@string/privacy_settings"
                android:fragment="com.android.settings.PrivacySettings"
                android:icon="@drawable/ic_settings_backup"
                />

    </dashboard-category>

    <!-- SYSTEM -->
    <dashboard-category
        android:id="@+id/system_section"
        android:title="@string/header_category_system" >

        <!-- Date & Time -->
        <dashboard-tile
                android:id="@+id/date_time_settings"
                android:title="@string/date_and_time_settings_title"
                android:fragment="com.android.settings.DateTimeSettings"
                android:icon="@drawable/ic_settings_date_time"
                />

        <!--Scheduled power on&off-->
        <dashboard-tile
                android:id="@+id/power_settings"
                android:icon="@drawable/ic_settings_schpwronoff"
                android:title="@string/schedule_power_on_off_settings_title">
            <intent android:action="com.android.settings.SCHEDULE_POWER_ON_OFF_SETTING" />
        </dashboard-tile>

        <!-- Accessibility feedback -->
        <dashboard-tile
                android:id="@+id/accessibility_settings"
                android:title="@string/accessibility_settings"
                android:fragment="com.android.settings.accessibility.AccessibilitySettings"
                android:icon="@drawable/ic_settings_accessibility"
                />

        <!-- Print -->
        <dashboard-tile
                android:id="@+id/print_settings"
                android:title="@string/print_settings"
                android:fragment="com.android.settings.print.PrintSettingsFragment"
                android:icon="@drawable/ic_settings_print"
                />

        <!-- Development -->
        <dashboard-tile
                android:id="@+id/development_settings"
                android:title="@string/development_settings_title"
                android:fragment="com.android.settings.DevelopmentSettings"
                android:icon="@drawable/ic_settings_development"
                />

        <!-- About Device -->
        <dashboard-tile
                android:id="@+id/about_settings"
                android:title="@string/about_settings"
                android:fragment="com.android.settings.DeviceInfoSettings"
                android:icon="@drawable/ic_settings_about"
                />

    </dashboard-category>

</dashboard-categories>

```

根据这个文件我们可以知道了，所谓的dashboard就是Settings模块首界面的一个抽象。而dashboard-categorys则是设置分类集合的抽象，而dashboard-category是分类的抽象，dashboard-tile就是分类下每个选项的抽象了。代码中的List<DashboardCategory>对应dashboard-categorys， DashboardCategory对应dashboard-category，而dashboard-tile则对因代码中的DashboardTile。

 

当加载完这些对象后SettingsActivity会将得到的 mCategories 返回给DashboardSummary来初始化Settings的设置选项。

 

 

下面这段代码就是DashboardSummary.rebuildUI()中完成界面的初始化

```
   long start = System.currentTimeMillis();

        final Resources res = getResources();

 

        mDashboard.removeAllViews();

 

        List<DashboardCategory> categories =

                ((SettingsActivity) context).getDashboardCategories(true);

 

        final int count = categories.size();

        for (int n = 0; n < count; n++) {

            DashboardCategory category = categories.get(n);

 

            View categoryView = mLayoutInflater.inflate(R.layout.dashboard_category, mDashboard,

                    false);

 

            TextView categoryLabel = (TextView) categoryView.findViewById(R.id.category_title);

            categoryLabel.setText(category.getTitle(res));

 

            ViewGroup categoryContent =

                    (ViewGroup) categoryView.findViewById(R.id.category_content);

 

            final int tilesCount = category.getTilesCount();

            for (int i = 0; i < tilesCount; i++) {

                DashboardTile tile = category.getTile(i);

 

                DashboardTileView tileView = new DashboardTileView(context);

                updateTileView(context, res, tile, tileView.getImageView(),

                        tileView.getTitleTextView(), tileView.getStatusTextView());

 

                tileView.setTile(tile);

 

                categoryContent.addView(tileView);

            }

 

            // Add the category

            mDashboard.addView(categoryView);

        }
```

这段代码我就不具体分析了，逻辑很简单，遍历categories这个列表来获取DashboardCategory对象，将所有DashboardCategory对象和DashboardCategory对象中的DashboardTile对象转化为视图对象并添加到主视图对象mDashboard中。

 

到这里SettingsActivity的onCreate方法就算结束了。总结一下，

   **1.onCreate完成的任务是切换DashboardSmmary这个Fragment，然后从dashboard_categories.xml中读取预先配置好的文件来初始化Settings的首界面视图。**
   **2.L中舍弃了Header类，取而代之的是DashboardCategory和DashboardTile类。**
   

> 转载请注明出处，谢谢~: http://blog.csdn.net/u011974987/article/details/51004854
