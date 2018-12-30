---
author: jeidee
categories:
- ios
date: '2015-06-02'
guid: https://erlnote.wordpress.com/?p=406
id: 406
permalink: /2015/06/02/ios-url-%ec%ba%90%ec%8b%9c-%ec%84%a4%ec%a0%95/
tags:
- URL Cache
title: ios url 캐시 설정
url: /2015/06/02/ios-url-ecba90ec8b9c-ec84a4eca095
---

```objc
  
// url 캐시 설정
  
// 메모리 : 10MB, 디스크 : 50MB
  
NSURLCache \*urlCache = [[NSURLCache alloc] initWithMemoryCapacity:10 \* 1024 \* 1024 diskCapacity:50 \* 1024 * 1024 diskPath:nil];
  
[NSURLCache setSharedURLCache:urlCache];

```

## 출처

  * [iphone에서 메모리 문제로 URL로 데이터를 가끔씩 못 가져올 때](http://egloos.zum.com/tiger5net/v/5710279)
  * [AFNetworking, NSURLCache로 이미지 캐시하기](http://daddy.areum.kr/?p=217)