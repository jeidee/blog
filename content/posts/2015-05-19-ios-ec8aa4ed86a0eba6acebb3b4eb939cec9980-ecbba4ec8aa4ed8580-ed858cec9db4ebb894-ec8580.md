---
author: jeidee
categories:
- ios
date: '2015-05-19'
guid: https://erlnote.wordpress.com/?p=393
id: 393
permalink: /2015/05/19/ios-%ec%8a%a4%ed%86%a0%eb%a6%ac%eb%b3%b4%eb%93%9c%ec%99%80-%ec%bb%a4%ec%8a%a4%ed%85%80-%ed%85%8c%ec%9d%b4%eb%b8%94-%ec%85%80/
tags:
- storyboard
- tablecell
- tableview
title: ios 스토리보드와 커스텀 테이블 셀
url: /2015/05/19/ios-ec8aa4ed86a0eba6acebb3b4eb939cec9980-ecbba4ec8aa4ed8580-ed858cec9db4ebb894-ec8580
---

스토리보드에 작성한 커스텀 테이블셀을 코드에서 사용하기 위해서는 다음과 같이 작업한다.

1) Attributes Inspector에서 Identifier를 설정한다.

2) Custom TableViewController.mm에서 다음과 같이 작성한다.

tableView의 dequeueReusableCellWithIdentifier 함수를 사용하면 된다.

```objc
  
&#8211; (UITableViewCell\*)tableView:(UITableView \*)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

static NSString *cellId = @"friendCell";

FriendCell\* cell = (FriendCell\*)[tableView dequeueReusableCellWithIdentifier:cellId];

```

## 출처

  * [스토리보드에서 테이블뷰 셀 가져오기](https://byunsooblog.wordpress.com/2014/09/19/ios-%EC%8A%A4%ED%86%A0%EB%A6%AC%EB%B3%B4%EB%93%9C%EC%97%90%EC%84%9C-%ED%85%8C%EC%9D%B4%EB%B8%94%EB%B7%B0-%EC%85%80-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0/)