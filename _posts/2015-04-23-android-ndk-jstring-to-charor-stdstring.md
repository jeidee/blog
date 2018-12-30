---
author: jeidee
categories:
- android
date: '2015-04-23'
guid: https://erlnote.wordpress.com/?p=321
id: 321
permalink: /2015/04/23/android-ndk-jstring-to-charor-stdstring/
tags:
- string
title: android ndk jstring to char*(or std::string)
url: /2015/04/23/android-ndk-jstring-to-charor-stdstring
---

```cpp
  
string toString(JNIEnv* env, jstring in) {
      
char* nativeStr;
      
const char* temp = env->GetStringUTFChars(in, 0);
      
nativeStr = strdup(temp);
      
env->ReleaseStringUTFChars(in, temp);

LOGD("toString() %s", nativeStr);
      
return string(nativeStr);
  
}
  
```