---
layout: post
title: Android 中取得当前时区（以GMT形式）和语言
permalink: Android-getdate-language
date: 2016-10-20 18:05:00
categories: Android
tags: Android 
desc: 国际化需求，要访问当地的时区和语言，作为参数上传服务器处理一些业务，Android手机中如果想以GMT形式（GMT+08:00）得到当前时区，如下的工具类做个记录：
---


国际化需求，要访问当地的时区和语言，作为参数上传服务器处理一些业务，Android手机中如果想以GMT形式（GMT+08:00）得到当前时区，如下的工具类做个记录：
```

/**
 * 时区语言工具类
 * @author Xiho
 *
 */
public class I18NUtils {

	/**
	 * 获取当前时区
	 * @return
	 */
	public static String getCurrentTimeZone() {
		TimeZone tz = TimeZone.getDefault();
		String strTz = tz.getDisplayName(false, TimeZone.SHORT);
		return strTz;

	}
	
	
	/**
	 * 获取当前系统语言格式
	 * @param mContext
	 * @return
	 */
	public static String getCurrentLanguage(Context mContext){
	    Locale locale =mContext.getResources().getConfiguration().locale;
        String language=locale.getLanguage();
        String country = locale.getCountry();
        String lc=language+"_"+country;
        return lc;
	}
	

}

```
<!-- more -->