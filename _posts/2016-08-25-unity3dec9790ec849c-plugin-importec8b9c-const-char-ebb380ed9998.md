---
author: jeidee
categories:
- c++
- csharp
- unity3d
date: '2016-08-25'
guid: https://erlnote.wordpress.com/?p=1607
id: 1607
permalink: /2016/08/25/unity3d%ec%97%90%ec%84%9c-plugin-import%ec%8b%9c-const-char-%eb%b3%80%ed%99%98/
tags:
- unity3d plugin
title: Unity3D에서 plugin import시 const char* 변환
url: /2016/08/25/unity3dec9790ec849c-plugin-importec8b9c-const-char-ebb380ed9998
---

다음 링크를 참고한다.
  
http://answers.unity3d.com/questions/160230/returning-variables-in-a-plugin.html

내용을 간략하게 요약하면 다음과 같다.

C/C++의 const char* 데이터타입을 C#으로 변환할 때는,
  
IntPtr로 받아서 Marshal.PtrToStringAnsi(&#8230;)함수를 통해 string으로 변환할 수 있다.