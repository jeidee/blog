---
id: 1610
title: 'Unity3D에서 iOS &#038; OSX Plugin 개발'
date: 2016-08-25T17:24:45+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1610
permalink: '/2016/08/25/unity3d%ec%97%90%ec%84%9c-ios-osx-plugin-%ea%b0%9c%eb%b0%9c/'
categories:
  - c++
  - ios
  - unity3d
tags:
  - unity3d plugin
---
다음과 같은 C++ 파일이 있다.

  * hello.h

```cpp
  
#ifndef hello_hpp
  
#define hello_hpp

#include <stdio.h>

class hello {
  
public:
      
char* getMessage();
  
};

extern "C" {
      
hello* createHello();
      
void deleteHello(hello* obj);

char\* getMessage(hello\* hello);
  
}

#endif /\* hello_hpp \*/
  
```

  * hello.cpp

```cpp
  
#include "hello.h"

char* hello::getMessage() {
      
return (char*)"Hello, world";
  
}

hello* createHello() {
      
return new hello();
  
}

void deleteHello(hello* obj) {
      
if (obj == NULL) return;
      
delete obj;
  
}

char\* getMessage(hello\* obj) {
      
if (obj == NULL) return NULL;

return obj->getMessage();
  
}
  
```

Unity3D에서 C++로 작성된 hello 클래스를 사용하는 방법을 살펴보자.

# OSX

OSX의 경우 bundle library를 생성해서 Unity의 Assets/Plugins 디렉토리에 xxx.bundle 파일을 복사해 넣어야 한다.

XCode에서 New > OS X> Framework & Library에서 Bundle 프로젝트를 생성한다.
  
프로젝트 이름은 hello로 입력한다.

hello.h/cpp 파일을 작성한 후 컴파일한다.
  
hello.bundle 파일이 생성되면 Unity3D의 Assets/Plugins 디렉토리에 hello.bundle을 복사해 넣는다.

Unity3D에서 C# 스크립트를 하나 작성한다.

  * Hello.cs

```csharp
  
using UnityEngine;
  
using System.Collections;
  
using System.Runtime.InteropServices;
  
using System;

public class Hello : MonoBehaviour {
  
#if UNITY_IOS
      
[DllImport ("__Internal")]
  
#else
      
[DllImport("hello")]
  
#endif
      
private static extern IntPtr createHello();

#if UNITY_IOS
      
[DllImport ("__Internal")]
  
#else
      
[DllImport("hello")]
  
#endif
      
private static extern void deleteHello(IntPtr obj);

#if UNITY_IOS
      
[DllImport ("__Internal")]
  
#else
      
[DllImport("hello")]
  
#endif
      
private static extern IntPtr getMessage(IntPtr obj);

// Use this for initialization
      
void Start () {
          
var obj = createHello();
          
message = Marshal.PtrToStringAnsi(getMessage(obj));
          
deleteHello(obj);
      
}

// Update is called once per frame
      
void Update () {

}

void OnGUI() {
          
GUI.Label(new Rect(10, 10, 100, 20), message);
      
}
  
}

```

Hello.cs의 경우 DllImport를 OS별로 달리 하도록 작성되었음을 확인한다.
  
Unity Editor에서는 hello.bundle을 import한다. (.bundle 확장자를 기입하지 않았음을 주의한다.)

# iOS

Hello.cs는 동일하며, hell.bundle 대신 Assets/Plugins/iOS 디렉토리에 hello.cpp와 hello.h 소스파일을 복사해 넣는다.

iOS 프로젝트를 빌드한 후 실행하면 정상 동작하는 것을 확인할 수 있다.
  
만약 3rd-party static library를 참조할 경우 해당 디바이스에 맞게(iOS) 빌드된 xxx.a 파일과 .h 파일들을 동일 디렉토리에 복사해 넣으면 된다.