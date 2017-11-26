---
id: 330
title: android ndk에서 stringstream을 쓸 경우 __atomic_fetch_add_4 관련 링크 에러가 발생
date: 2015-04-29T13:51:25+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=330
permalink: '/2015/04/29/android-ndk%ec%97%90%ec%84%9c-stringstream%ec%9d%84-%ec%93%b8-%ea%b2%bd%ec%9a%b0-__atomic_fetch_add_4-%ea%b4%80%eb%a0%a8-%eb%a7%81%ed%81%ac-%ec%97%90%eb%9f%ac%ea%b0%80-%eb%b0%9c%ec%83%9d/'
categories:
  - android
tags:
  - error
  - ndk
---
Android.mk에 다음과 같이 추가한다.

```
  
LOCAL_LDLIBS += -latomic
  
```

## 출처

  * [Android NDK STL c++\_shared w/LIBCXX\_FORCE_REBUILD results in std::stringstream NOP](http://stackoverflow.com/questions/23041401/android-ndk-stl-c-shared-w-libcxx-force-rebuild-results-in-stdstringstream-n)