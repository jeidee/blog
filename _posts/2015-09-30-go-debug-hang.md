---
id: 598
title: go debug hang
date: 2015-09-30T17:18:24+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=598
permalink: /2015/09/30/go-debug-hang/
categories:
  - go
tags:
  - debug
  - hang
---
go로 만든 어플리케이션이 hang에 걸렸을 경우 다음과 같이 해당 프로세스의 스택을 추적할 수 있다.

```
  
$ kill -ABRT process-id
  
```