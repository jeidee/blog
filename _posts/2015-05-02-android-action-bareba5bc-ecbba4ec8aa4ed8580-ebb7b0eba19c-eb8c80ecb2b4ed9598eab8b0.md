---
id: 332
title: android action bar를 커스텀 뷰로 대체하기
date: 2015-05-02T09:25:10+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=332
permalink: '/2015/05/02/android-action-bar%eb%a5%bc-%ec%bb%a4%ec%8a%a4%ed%85%80-%eb%b7%b0%eb%a1%9c-%eb%8c%80%ec%b2%b4%ed%95%98%ea%b8%b0/'
categories:
  - android
tags:
  - ActionBar
---
Action Bar에 버튼을 추가하거나 삭제하는 등의 편집을 위해서는 [menu/menu_xxx.xml을 편집](https://developer.android.com/training/basics/actionbar/adding-buttons.html)하면 된다.

조금 더 다양한 레이아웃을 사용하기 위해서는 [커스텀 뷰를 만들어](http://javatechig.com/android/actionbar-with-custom-view-example-in-android) 사용해야 한다.

커스텀 뷰를 만들고 Action Bar의 높이 사이즈가 맞지 않을 경우 다음과 같이 styles.xml에 추가하도록 한다.

```
      
<style name="AppTheme" parent="@android:style/Theme.Holo.Light">
          
<item name="android:attr/actionBarSize">120dp</item>
      
</style>
  
```

## 참고

  * [ActionBar with Custom View Example in Android](http://javatechig.com/android/actionbar-with-custom-view-example-in-android)
  * [Adding Action Button](https://developer.android.com/training/basics/actionbar/adding-buttons.html)