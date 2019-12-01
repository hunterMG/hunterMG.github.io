---
title: android-auto-script
date: 2018-08-15 11:47:18
categories: Android
tags:
- note
- android
- reading list
---
Note while developing an Android script to auto comment in an APP.
<!-- more -->

## Floating Window
https://www.jianshu.com/p/ac63c57d2555 (Global)
https://www.jianshu.com/p/95ceb0a2ed27  
https://github.com/yhaolpz/FloatWindow (can only be used in an APP)  
## Decompile APK
https://blog.csdn.net/guolin_blog/article/details/49738023

## adb shell input text '你好'
https://github.com/senzhk/ADBKeyBoard

## execute shell comands in Java code
https://github.com/jaredrummler/AndroidShell

## CountDownTimer
`CountDownTimer` must be canceled explicitly in `onDestroy` in a `Service`.
```java
if (countDownTimer1 != null){
    countDownTimer1.cancel();
}
```