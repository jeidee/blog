---
author: jeidee
categories:
- ios
date: '2015-05-19'
guid: https://erlnote.wordpress.com/?p=396
id: 396
permalink: /2015/05/19/ios-%eb%b9%84%eb%8f%99%ea%b8%b0%eb%a1%9c-%ec%9d%b4%eb%af%b8%ec%a7%80-%eb%8b%a4%ec%9a%b4%eb%a1%9c%eb%93%9c/
tags:
- cache
- dispatch_async
- image
title: ios 비동기로 이미지 다운로드
url: /2015/05/19/ios-ebb984eb8f99eab8b0eba19c-ec9db4ebafb8eca780-eb8ba4ec9ab4eba19ceb939c
---

이미지캐시 클래스를 만들고 있는 과정인데 우선 비동기로 이미지를 다운로드 하는 코드를 작성해 보았다.
  
아직 캐시 기능은 구현하지 않았다.

ImageCache.h

```objc
  
#import <UIKit/UIKit.h>

@interface ImageCache : NSObject

+(ImageCache*)getInstance;

-(void) loadFromUrl: (NSURL\*) url callback:(void (^)(UIImage \*image))callback;

@end
  
```

ImageCache.m

```objc
  
#import <UIKit/UIKit.h>

@interface ImageCache : NSObject

+(ImageCache*)getInstance;

-(void) loadFromUrl: (NSURL\*) url callback:(void (^)(UIImage \*image))callback;

@end
  
```

사용

```objc
      
[[ImageCache getInstance] loadFromUrl:roster.photo callback:^(UIImage *image) {
          
cell.imgAvatar.image = image;
      
}];
  
```

## 참고

  * [Loading an image into UIImage asynchronously](http://stackoverflow.com/questions/9786018/loading-an-image-into-uiimage-asynchronously)
  * [iphone에서 메모리 문제로 URL로 데이터를 가끔씩 못 가져올 때](http://egloos.zum.com/tiger5net/v/5710279)
  * [Disk Caching with AFNetworking](http://nicemohawk.com/blog/2014/03/disk-caching-with-afnetworking/)
  * [UIImageView에 원격이미지 비동기 로드 및 캐쉬 기능 넣기](http://jidolstar.tistory.com/723)