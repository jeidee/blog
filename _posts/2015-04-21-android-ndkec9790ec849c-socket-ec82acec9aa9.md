---
id: 313
title: Android NDK에서 socket 사용
date: 2015-04-21T19:19:08+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=313
permalink: '/2015/04/21/android-ndk%ec%97%90%ec%84%9c-socket-%ec%82%ac%ec%9a%a9/'
categories:
  - android
tags:
  - socket
---
AndroidMenifest.xml에 다음과 같이 추가한다.

```
      
<uses-permission android:name="android.permission.INTERNET" />
      
<application
  
```

주의할 것은 application 태그 위에 입력해야 한다는 것이다.

## 출처

  * [Using Socket() in Android NDK](http://stackoverflow.com/questions/6033581/using-socket-in-android-ndk)