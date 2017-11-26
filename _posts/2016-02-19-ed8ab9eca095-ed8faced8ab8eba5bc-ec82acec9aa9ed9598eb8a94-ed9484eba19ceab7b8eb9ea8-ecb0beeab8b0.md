---
id: 1035
title: 특정 포트를 사용하는 프로그램 찾기
date: 2016-02-19T11:22:02+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1035
permalink: '/2016/02/19/%ed%8a%b9%ec%a0%95-%ed%8f%ac%ed%8a%b8%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%98%eb%8a%94-%ed%94%84%eb%a1%9c%ea%b7%b8%eb%9e%a8-%ec%b0%be%ea%b8%b0/'
categories:
  - ejabberd
  - utility
---
```
  
netstat -tulpn | grep 12345

-t | &#8211;tcp
  
-u | &#8211;udp
  
-l | &#8211;listening
  
-p | &#8211;programs
  
-n | &#8211;numeric (don't resolve name)
  
```