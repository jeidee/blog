---
id: 321
title: 'android ndk jstring to char*(or std::string)'
date: 2015-04-23T21:04:47+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=321
permalink: /2015/04/23/android-ndk-jstring-to-charor-stdstring/
categories:
  - android
tags:
  - string
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