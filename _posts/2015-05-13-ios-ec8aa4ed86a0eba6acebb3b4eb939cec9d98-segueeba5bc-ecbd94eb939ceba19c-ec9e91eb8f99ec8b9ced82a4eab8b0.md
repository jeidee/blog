---
author: jeidee
categories:
- ios
date: '2015-05-13'
guid: https://erlnote.wordpress.com/?p=359
id: 359
permalink: /2015/05/13/ios-%ec%8a%a4%ed%86%a0%eb%a6%ac%eb%b3%b4%eb%93%9c%ec%9d%98-segue%eb%a5%bc-%ec%bd%94%eb%93%9c%eb%a1%9c-%ec%9e%91%eb%8f%99%ec%8b%9c%ed%82%a4%ea%b8%b0/
tags:
- segue
- storyboard
title: ios 스토리보드의 segue를 코드로 작동시키기
url: /2015/05/13/ios-ec8aa4ed86a0eba6acebb3b4eb939cec9d98-segueeba5bc-ecbd94eb939ceba19c-ec9e91eb8f99ec8b9ced82a4eab8b0
---

스토리보드(Main.storyboard)를 다음과 같이 작성했다고 가정해 보자.

  * =>(Entry) Login View Controller =>(Segue id: moveToTabBar) Tab Bar Controller

Entry 뷰는 Login View이다.
  
Login View에서 Tab Bar Controller로 이동하는 seque를 생성한 후 Attributes Inspector에서 Identifier값을 &#8220;moveToTabBar&#8221;로 수정한다.

그리고 이동하고자 하는 지점의 코드에서 다음과 같이 작성하면 된다.

```objc
  
[self performSegueWithIdentifier:@"moveToTabController" sender:nil];
  
```