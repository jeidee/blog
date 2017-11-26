---
id: 343
title: android orientation이 변경될 때 감지하는 방법
date: 2015-05-04T21:23:33+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=343
permalink: '/2015/05/04/android-orientation%ec%9d%b4-%eb%b3%80%ea%b2%bd%eb%90%a0-%eb%95%8c-%ea%b0%90%ec%a7%80%ed%95%98%eb%8a%94-%eb%b0%a9%eb%b2%95/'
categories:
  - android
tags:
  - orientation
---
별도의 작업 없이는 orientation이 변경될 때 감지할 수 없다.

orientation 변경을 감지할 수 있는 방법은 다음과 같이 두 가지가 있다.

1) Activity에서 단순히 landscape와 portrait감지만 필요한 경우

감지할 Activity에서 onConfigurationChanged() 함수를 override한다.

```java
      
@Override
      
public void onConfigurationChanged(Configuration newConfig) {
          
super.onConfigurationChanged(newConfig);

// Checks the orientation of the screen
          
if (newConfig.orientation == Configuration.ORIENTATION_LANDSCAPE) {
              
Toast.makeText(this, "landscape", Toast.LENGTH_SHORT).show();
          
} else if (newConfig.orientation == Configuration.ORIENTATION_PORTRAIT){
              
Toast.makeText(this, "portrait", Toast.LENGTH_SHORT).show();
          
}
      
}

```

위와 같이 추가한 후 빌드해서 실행해보면 제대로 동작하지 않는다.
  
다음과 같이 AndroidManifest.xml에서 해당 Activity의 cofigChanges attribute에 명시해줘야 한다.

```
          
<activity
              
android:name=".MainActivity"
              
android:configChanges="orientation|screenSize"
  
```

2) 상세한 방향 전환을 감지할 필요가 있는 경우
  
orientation의 값을 정밀하게 제어할 필요가 있는 경우 OrientationListener를 사용한다.

```java
  
OrientationEventListener orientationListener = new OrientationEventListener(context, SensorManager.SENSOR\_DELAY\_UI) {
          
public void onOrientationChanged(int orientation) {
          
}
      
};

if (orientationListener.canDetectOrientation()) {
      
orientationListener.enable();
  
}
  
```