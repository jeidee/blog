---
id: 341
title: android cannot load library error
date: 2015-05-04T15:12:33+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=341
permalink: /2015/05/04/android-cannot-load-library-error/
categories:
  - android
tags:
  - error
  - ndk
---
ndk build후 의존성 있는 .so 라이브러리를 일부 폰에서 로드하지 못할 경우 다음과 같이 수정해 본다.

```java
      
static {
          
System.loadLibrary("gloox_lib");
          
System.loadLibrary("msg_lib");
      
}
  
```

일부 폰(sdk 22)에서는 msg\_lib만 로드하면 되지만, 일부 폰(sdk 16)에서는 msg\_lib가 의존하는 gloox_lib를 로드하지 못하는 경우가 발생한다.
  
이럴 경우 위와 같이 의존성 있는 라이브러리를 함께 로드해주면 된다.