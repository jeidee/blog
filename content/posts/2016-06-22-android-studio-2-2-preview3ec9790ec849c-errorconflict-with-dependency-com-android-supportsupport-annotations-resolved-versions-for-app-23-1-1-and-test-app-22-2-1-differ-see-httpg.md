---
author: jeidee
categories:
- android
date: '2016-06-22'
guid: https://erlnote.wordpress.com/?p=1461
id: 1461
permalink: /2016/06/22/android-studio-2-2-preview3%ec%97%90%ec%84%9c-errorconflict-with-dependency-com-android-supportsupport-annotations-resolved-versions-for-app-23-1-1-and-test-app-22-2-1-differ-see-httpg/
title: Android Studio 2.2 Preview3에서 Error:Conflict with dependency &#8216;com.android.support:support-annotations&#8217;. Resolved versions for app (23.1.1) and test app (22.2.1) differ. See http://g.co/androidstudio/app-test-app-conflict for details.
url: /2016/06/22/android-studio-2-2-preview3ec9790ec849c-errorconflict-with-dependency-com-android-supportsupport-annotations-resolved-versions-for-app-23-1-1-and-test-app-22-2-1-differ-see-httpg
---

build.gradle(Module: app)에 다음을 추가한다.

```
  
android {
      
compileSdkVersion 22
      
buildToolsVersion "23.0.2"
      
&#8230;

configurations.all {
          
resolutionStrategy {
              
force 'com.android.support:support-annotations:23.1.1'
          
}
      
}
  
}
  
```

  * [참고글](https://github.com/square/assertj-android/issues/193)