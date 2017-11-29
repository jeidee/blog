---
author: jeidee
categories:
- ios
date: '2015-11-04'
guid: https://erlnote.wordpress.com/?p=602
id: 602
permalink: /2015/11/04/ios-sqlite-swift%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-%ec%9d%bd%ea%b8%b0-%ec%93%b0%ea%b8%b0-db-%ec%97%b4%ea%b8%b0/
tags:
- sqlite
- swift
title: ios sqlite.swift를 사용해서 읽기 쓰기 DB 열기
url: /2015/11/04/ios-sqlite-swifteba5bc-ec82acec9aa9ed95b4ec849c-ec9dbdeab8b0-ec93b0eab8b0-db-ec97b4eab8b0
---

```swift
  
do {
              
let path = NSSearchPathForDirectoriesInDomains(.DocumentDirectory, .UserDomainMask, true).first!

let db = try Connection("\(path)/db.sqlite3")
          
} catch {
              
NSLog("DB Connection error!")

}
  
```