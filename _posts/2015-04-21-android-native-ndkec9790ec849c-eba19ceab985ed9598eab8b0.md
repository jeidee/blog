---
id: 310
title: android native ndk에서 로깅하기
date: 2015-04-21T19:03:59+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=310
permalink: '/2015/04/21/android-native-ndk%ec%97%90%ec%84%9c-%eb%a1%9c%ea%b9%85%ed%95%98%ea%b8%b0/'
categories:
  - android
tags:
  - log
  - ndk
---
다음과 같이 선언하고 printf() 사용하듯이 사용하면 된다.

```c
  
#include <android/log.h>

#define LOG_TAG "someTag"

#define LOGE(&#8230;) \_\_android\_log\_print(ANDROID\_LOG\_ERROR,LOG\_TAG,\\_\_VA\_ARGS\__)
  
#define LOGW(&#8230;) \_\_android\_log\_print(ANDROID\_LOG\_WARN,LOG\_TAG,\\_\_VA\_ARGS\__)
  
#define LOGD(&#8230;) \_\_android\_log\_print(ANDROID\_LOG\_DEBUG,LOG\_TAG,\\_\_VA\_ARGS\__)
  
#define LOGI(&#8230;) \_\_android\_log\_print(ANDROID\_LOG\_INFO,LOG\_TAG,\\_\_VA\_ARGS\__)
  
```

## 출처

  * [Logging values of variables in Android native ndk](http://stackoverflow.com/questions/12159316/logging-values-of-variables-in-android-native-ndk)