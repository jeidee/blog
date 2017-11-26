---
id: 404
title: ios UIImage 썸네일 이미지 만들기
date: 2015-06-02T10:12:12+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=404
permalink: '/2015/06/02/ios-uiimage-%ec%8d%b8%eb%84%a4%ec%9d%bc-%ec%9d%b4%eb%af%b8%ec%a7%80-%eb%a7%8c%eb%93%a4%ea%b8%b0/'
categories:
  - ios
tags:
  - thumbnail
  - UIImage
---
```objc
  
-(UIImage\*)makeThumbnail:(UIImage\*)image withSize:(CGSize)destSize{
      
UIGraphicsBeginImageContext(destSize);
      
[image drawInRect:CGRectMake(0, 0, destSize.width, destSize.height)];

UIImage* thumbnail = UIGraphicsGetImageFromCurrentImageContext();

UIGraphicsEndImageContext();

return thumbnail;
  
}
  
```

## 출처

  * [HOW TO CREATE THUMBNAIL OF UIIMAGE – XCODE IOS](http://beageek.biz/how-to-create-thumbnail-uiimage-xcode-ios/)