---
id: 316
title: android ndk, native c/c++ 코드에서 java 코드 호출
date: 2015-04-22T17:58:01+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=316
permalink: '/2015/04/22/android-ndk-native-cc-%ec%bd%94%eb%93%9c%ec%97%90%ec%84%9c-java-%ec%bd%94%eb%93%9c-%ed%98%b8%ec%b6%9c/'
categories:
  - android
tags:
  - callback
  - ndk
---
다음과 같이 두 가지 측면에서 접근할 수 있다.

  * 동일 쓰레드에서 jni call(java코드에서 native c/c++ 함수 호출)을 하고, 동일 스코프에서 java object의 함수를 호출하는 경우
  * 호출측(java) 쓰레드와 콜백(native c/c++) 쓰레드가 다른 경우

## 동일 쓰레드에서 콜백 구현

java

```java
  
package com.jeidee.glooxforandroid;

public TestClient {
      
public native callbackTest();

public void testCallback(String param) {
          
Log.d("JNI", param);
      
}
  
}
  
```

native c++

```cpp
  
include "com\_jeidee\_glooxforandroid_NativeCall.h"

#include "client.h"

JNIEXPORT void JNICALL Java\_com\_jeidee\_glooxforandroid\_NativeCall_callbackTest (JNIEnv *env, jobject obj) {
      
static jmethodID cb = NULL;
      
jclass cls = env->GetObjectClass(obj);

if (cb == NULL) {
          
cb = env->GetMethodID(cls, "testCallback", "(Ljava/lang/String;)V");
          
if (cb == NULL) return;
      
}

env->CallVoidMethod(obj, cb, env->NewStringUTF("call testCallback()"));
  
}
  
```

JNI함수인 callbackTest()를 호출한 java측 object에 testCallback(String) 함수가 있을 경우 위의 코드는 정상 동작한다.

## 서로 다른 쓰레드에서 콜백 구현

예를 들어, 소켓 클라이언트를 native c/c++ 코드로 구현했다고 가정해 보자.
  
클라이언트가 서버에 접속 후 서버에서 데이터를 수신했을때 java 코드에 콜백한다고 한다면,
  
데이터 수신하는 쓰레드(보통은 native 측에서 쓰레드 구현)와 호출측(java ui 쓰레드)의 쓰레드는 다르다.

이럴 경우 간단하게 콜백 받을 java측의 클래스에 static method를 생성해 다음과 같이 구현할 수 있다.

java

```java
  
public TestClient {
      
public native callbackTest();

public static void testCallback(String param) {
          
Log.d("JNI", param);
      
}
  
}
  
```

native c++

```cpp
  
JNIEnv\* getJNIEnv(JavaVM\* jvm)
  
{

if (NULL == jvm) {
          
LOGD("Failed to get JNIEnv. JniHelper::getJavaVM() is NULL");
          
return NULL;
      
}

JNIEnv *env = NULL;
      
// get jni environment
      
jint ret = jvm->GetEnv((void**)&env, JNI\_VERSION\_1_4);

switch (ret) {
          
case JNI_OK :
              
// Success!
              
return env;

case JNI_EDETACHED :
              
// Thread not attached

// TODO : If calling AttachCurrentThread() on a native thread
              
// must call DetachCurrentThread() in future.
              
// see: http://developer.android.com/guide/practices/design/jni.html

if (jvm->AttachCurrentThread(&env, NULL) < 0)
              
{
                  
LOGD("Failed to get the environment using AttachCurrentThread()");
                  
return NULL;
              
} else {
                  
// Success : Attached and obtained JNIEnv!
                  
return env;
              
}

case JNI_EVERSION :
              
// Cannot recover from this error
              
LOGD("JNI interface version 1.4 not supported");
          
default :
              
LOGD("Failed to get the environment using GetEnv()");
              
return NULL;
      
}
  
}

// get class and make it a global reference, release it at endJni().
  
jclass getClassID(JNIEnv \*pEnv, const char\* className)
  
{
      
jclass ret = pEnv->FindClass(className);
      
if (! ret)
      
{
          
LOGD("Failed to find class of %s", className);
      
}

return ret;
  
}

bool getStaticMethodInfo(JavaVM\* jvm, JniMethodInfo &methodinfo, const char\* className, const char \*methodName, const char \*paramCode)
  
{
      
jmethodID methodID = 0;
      
JNIEnv *pEnv = 0;
      
bool bRet = false;

do
      
{
          
pEnv = getJNIEnv(jvm);
          
if (! pEnv)
          
{
              
break;
          
}

jclass classID = getClassID(pEnv, className);

methodID = pEnv->GetStaticMethodID(classID, methodName, paramCode);
          
if (! methodID)
          
{
              
LOGD("Failed to find static method id of %s", methodName);
              
break;
          
}

methodinfo.classID = classID;
          
methodinfo.env = pEnv;
          
methodinfo.methodID = methodID;

bRet = true;
      
} while (0);

return bRet;
  
}

void callback(JavaVM\* jvm, const char\* value){
      
JniMethodInfo methodInfo;
      
if (! getStaticMethodInfo(jvm, methodInfo, "com/jeidee/glooxforandroid/TestClient", "testCallback", "(Ljava/lang/String;)V"))
      
{
          
return;
      
}

jstring stringArg = methodInfo.env->NewStringUTF(value);
      
methodInfo.env->CallStaticVoidMethod(methodInfo.classID, methodInfo.methodID, stringArg);
      
methodInfo.env->DeleteLocalRef(stringArg);
      
methodInfo.env->DeleteLocalRef(methodInfo.classID);
  
}

&#8230;

callback(m_jvm, "call testCallback()");

```

m_jvm은 JavaVM의 인스턴스이며 jni 로드시 다음과 같이 미리 구해놓을 수 있다.

```cpp
  
static JavaVM* g_JVM;

jint JNI_OnLoad(JavaVM \*jvm, void \*reserved)
  
{
      
LOGD("JNI_OnLoad");
      
g_JVM = jvm;

JNIEnv* env;
      
if (jvm->GetEnv((void **)&env, JNI\_VERSION\_1\_4) != JNI\_OK) {
          
LOGD("GETENVFAILEDONLOAD");
          
return -1;
      
}
      
return JNI\_VERSION\_1_4;
  
}

JNIEXPORT void JNICALL Java\_com\_jeidee\_glooxforandroid\_NativeCall_callbackTest (JNIEnv *env, jobject obj) {

&#8230;

```

필요할 경우 콜백함수측의 java 클래스를 싱글턴으로 구현해 놓고 사용하고 Handler를 사용해 ui 쓰레드와 통신하면 된다.

## 참고

  * [JNI에서 콜백함수 구현](http://blog.daum.net/_blog/BlogTypeView.do?blogid=0RBJ8&articleno=8&_bloghome_menu=recenttext)
  * [How to create callbacks between android code and native code?](http://stackoverflow.com/questions/13377168/how-to-create-callbacks-between-android-code-and-native-code)