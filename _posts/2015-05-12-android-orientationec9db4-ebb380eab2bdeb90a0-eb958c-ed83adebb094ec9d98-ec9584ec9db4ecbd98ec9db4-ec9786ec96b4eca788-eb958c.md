---
author: jeidee
categories:
- android
date: '2015-05-12'
guid: https://erlnote.wordpress.com/?p=352
id: 352
permalink: /2015/05/12/android-orientation%ec%9d%b4-%eb%b3%80%ea%b2%bd%eb%90%a0-%eb%95%8c-%ed%83%ad%eb%b0%94%ec%9d%98-%ec%95%84%ec%9d%b4%ec%bd%98%ec%9d%b4-%ec%97%86%ec%96%b4%ec%a7%88-%eb%95%8c/
tags:
- ActionBar
- tab
title: android orientation이 변경될 때 탭바의 아이콘이 없어질 때
url: /2015/05/12/android-orientationec9db4-ebb380eab2bdeb90a0-eb958c-ed83adebb094ec9d98-ec9584ec9db4ecbd98ec9db4-ec9786ec96b4eca788-eb958c
---

최신 android 버전(API 22)에서는 문제가 없어 보이지만 특정 버전(API 16, Android 4.1.2)에서 아이콘이 사라지는 문제가 발생한다.

해결책은 AndriodManifest.xml에서 해당 Activity의 configChanges 어트리뷰트에서 orientation을 제거하면 되는데,
  
근본적인 해결책이라고 볼 순 없는 것 같다.

```
          
<activity
              
android:name=".ui.MainActivity"
              
<!&#8211;android:configChanges="orientation|screenSize"&#8211;>
              
android:label="@string/app_name" >
          
</activity>
  
```

## 참고

  * [Custom tab view vanishes after orientation change](https://github.com/JakeWharton/ActionBarSherlock/issues/365)