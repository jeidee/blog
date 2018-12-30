---
author: jeidee
categories:
- android
date: '2015-04-29'
guid: https://erlnote.wordpress.com/?p=330
id: 330
permalink: /2015/04/29/android-ndk%ec%97%90%ec%84%9c-stringstream%ec%9d%84-%ec%93%b8-%ea%b2%bd%ec%9a%b0-__atomic_fetch_add_4-%ea%b4%80%eb%a0%a8-%eb%a7%81%ed%81%ac-%ec%97%90%eb%9f%ac%ea%b0%80-%eb%b0%9c%ec%83%9d/
tags:
- error
- ndk
title: android ndk에서 stringstream을 쓸 경우 __atomic_fetch_add_4 관련 링크 에러가 발생
url: /2015/04/29/android-ndkec9790ec849c-stringstreamec9d84-ec93b8-eab2bdec9ab0-__atomic_fetch_add_4-eab480eba0a8-eba781ed81ac-ec9790eb9faceab080-ebb09cec839d
---

Android.mk에 다음과 같이 추가한다.

```
  
LOCAL_LDLIBS += -latomic
  
```

## 출처

  * [Android NDK STL c++\_shared w/LIBCXX\_FORCE_REBUILD results in std::stringstream NOP](http://stackoverflow.com/questions/23041401/android-ndk-stl-c-shared-w-libcxx-force-rebuild-results-in-stdstringstream-n)