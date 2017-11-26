---
id: 336
title: android Circle Image View
date: 2015-05-02T09:31:11+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=336
permalink: /2015/05/02/android-circle-image-view/
categories:
  - android
tags:
  - circled
  - ImageView
  - rounded
---
ImageView를 round처리하거나 원형으로 처리하고자 할 때 사용할 수 있는 방법은 다음과 같다.

먼저 [RoundedAvatarDrawable](https://github.com/chrisbanes/philm/blob/master/app/src/main/java/app/philm/in/drawable/RoundedAvatarDrawable.java) 클래스를 다운로드 받아 프로젝트에 추가한다.

레이아웃에 ImageView를 하나 추가한 후 Activity의 onCreate()함수 안에서 다음과 같이 코딩한다.

```java
          
BitmapDrawable bImage = (BitmapDrawable) getResources().getDrawable(R.drawable.unnamed);
          
ImageView mImg = (ImageView) mCustomView.findViewById(R.id.imageView1);
          
mImg.setImageDrawable(new RoundedAvatarDrawable(bImage.getBitmap()));
  
```

## 참고

  * [How can i use RoundedAvatarDrawable for creating round image?](http://stackoverflow.com/questions/26630630/how-can-i-use-roundedavatardrawable-for-creating-round-image)
  * [효율적인 Bitmap 이미지 라운딩 처리 방법](http://www.kmshack.kr/%ED%9A%A8%EC%9C%A8%EC%A0%81%EC%9D%B8-bitmap-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%9D%BC%EC%9A%B4%EB%94%A9-%EC%B2%98%EB%A6%AC%EB%B0%A9%EB%B2%95/)
  * [네모난 비트맵을 둥글게 사용하기](http://chiwoos.tistory.com/185)