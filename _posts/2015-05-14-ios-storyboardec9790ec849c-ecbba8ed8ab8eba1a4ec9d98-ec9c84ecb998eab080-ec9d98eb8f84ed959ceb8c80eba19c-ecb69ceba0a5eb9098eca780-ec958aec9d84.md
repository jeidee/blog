---
id: 361
title: ios storyboard에서 컨트롤의 위치가 의도한대로 출력되지 않을 때
date: 2015-05-14T10:47:55+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=361
permalink: '/2015/05/14/ios-storyboard%ec%97%90%ec%84%9c-%ec%bb%a8%ed%8a%b8%eb%a1%a4%ec%9d%98-%ec%9c%84%ec%b9%98%ea%b0%80-%ec%9d%98%eb%8f%84%ed%95%9c%eb%8c%80%eb%a1%9c-%ec%b6%9c%eb%a0%a5%eb%90%98%ec%a7%80-%ec%95%8a%ec%9d%84/'
categories:
  - ios
tags:
  - auto layout
  - storyboard
---
Auto Layout을 사용해 컨트롤에 Constraints를 추가해도 의도한대로 출력되지 않는 경우가 있다.
  
이럴 경우 다음과 같이 스토리보드 레이아웃이 제대로 설정되어 있는지 확인한다.

![stortyboard_layout](http://localhost:8888/wordpress/wp-content/uploads/2015/05/ec8aa4ed81aceba6b0ec83b7-2015-05-14-ec98a4eca084-10-39-53.png)

위 이미지의 설정은 iPhone의 각 스크린 인치별 portrait와 landscape를 지원한다.

## 참고

  * [Auto Layout #2. Auto Layout with Storyboard](http://meetkei.com/?p=3460)