---
id: 1567
title: ios UITableView 여분의 셀 구분선을 제거 하는 방법
date: 2016-07-11T21:35:25+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1567
permalink: '/2016/07/11/ios-uitableview-%ec%97%ac%eb%b6%84%ec%9d%98-%ec%85%80-%ea%b5%ac%eb%b6%84%ec%84%a0%ec%9d%84-%ec%a0%9c%ea%b1%b0-%ed%95%98%eb%8a%94-%eb%b0%a9%eb%b2%95/'
categories:
  - ios
tags:
  - UITableView
---
UITableView의 기본설정은 데이터가 없을 때에도 셀 구분선이 출력된다.
  
Footer View를 다음과 같이 지정해서 제거할 수 있다.

```swift
  
tvList.tableFooterView = UIView()
  
```

http://stackoverflow.com/questions/1369831/eliminate-extra-separators-below-uitableview