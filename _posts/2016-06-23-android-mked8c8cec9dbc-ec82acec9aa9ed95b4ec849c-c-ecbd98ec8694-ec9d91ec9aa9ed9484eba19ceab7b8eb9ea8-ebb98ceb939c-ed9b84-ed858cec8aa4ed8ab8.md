---
id: 1470
title: Android.mk파일 사용해서 C++ 콘솔 응용프로그램 빌드 후 테스트
date: 2016-06-23T17:56:08+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1470
permalink: '/2016/06/23/android-mk%ed%8c%8c%ec%9d%bc-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-c-%ec%bd%98%ec%86%94-%ec%9d%91%ec%9a%a9%ed%94%84%eb%a1%9c%ea%b7%b8%eb%9e%a8-%eb%b9%8c%eb%93%9c-%ed%9b%84-%ed%85%8c%ec%8a%a4%ed%8a%b8/'
categories:
  - android
---
## 목표

ndk-build를 사용해 안드로이드 C++ 콘솔 응용프로그램을 빌드 후 디바이스에 배포해서 실행 테스트한다.

## 디렉토리 구조

다음과 같은 구조를 갖도록 디렉토리와 빈 파일을 생성한다.

```
  
ndktest (Dir)
  
&#8212; jni (Dir)
     
&#8212; Android.mk (File)
     
&#8212; Application.mk (File)
     
&#8212; hello.cpp (File)
  
```

## hello.cpp

```
  
#include <iostream>

using namespace std;

int main() {
          
cout << "Hello, World!" << endl;
          
return 0;
  
}
  
```

## Application.mk

stl을 사용하기 위해 다음과 같이 입력한다.

```
  
APP\_STL := stlport\_static
  
```

## Android.mk

```
  
LOCAL_PATH := $(call my-dir)
  
include $(CLEAR_VARS)

LOCAL_MODULE := helloworld

LOCAL_CFLAGS := -fPIE
  
LOCAL_CPPFLAGS := -fPIE
  
LOCAL_LDFLAGS := -fPIE -pie
  
LOCAL\_SRC\_FILES := \
          
hello.cpp

include $(BUILD_EXECUTABLE)
  
```

  * LOCAL\_PATH : $(call my-dir)은 현재 경로를 LOCAL\_PATH에 저장한다.
  * include $(CLEAR\_VARS) : LOCAL\_PATH를 제외한 LOCAL_XXX 변수를 초기화한다.
  * LOCAL_MODULE : 출력물의 이름이다. 실행파일일 경우 실행파일명이며 라이브러리일 경우 라이브러리명이다.
  * LOCAL_CFLAGS : gcc의 c 파일 컴파일 옵션이다. 
  * LOCAL_CPPFLAGS : gcc의 c++ 파일 컴파일 옵션이다.
  * LOCAL_LDFLAGS : gcc의 링커 옵션이다.
  * -fPIE는 Position Independent Executables 지원을 위한 옵션이다.
  * LOCAL\_SRC\_FILES : 소스파일 리스트이다. 기준경로는 LOCAL_PATH이다.
  * include $(BUILD\_EXECUTABLE) : 출력물이 실행파일일 경우 BUILD\_EXECUTABLE, 공유 라이브러리일 경우 BUILD\_SHARED\_LIBRARY, 정적 라이브러리일 경우 BUILD\_STATIC\_LIBRARY를 지정한다.

## 빌드

ndktest 디렉토리에서 다음 명령을 실행한다.
  
ndk-build는 Android NDK를 다운로드 받아 설치한 경로를 PATH에 지정하여야 한다.

```
  
$ ndk-build
  
```

## 디바이스에서 실행

adb는 Android SDK를 설치한 후 paltform-tools 경로를 PATH에 지정해야 사용할 수 있다.

```
  
$ adb push libs/armeabi/helloworld /data/local/tmp
  
$ adb shell /data/local/tmp/helloworld
  
```

다음과 같은 에러가 발생 시 -fPIE옵션을 지정해서 다시 컴파일 하도록 한다.

```
  
error: only position independent executables (PIE) are supported.
  
```