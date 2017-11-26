---
id: 1536
title: tsung을 사용해 ejabberd 테스트하기
date: 2016-06-28T14:20:14+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1536
permalink: '/2016/06/28/tsung%ec%9d%84-%ec%82%ac%ec%9a%a9%ed%95%b4-ejabberd-%ed%85%8c%ec%8a%a4%ed%8a%b8%ed%95%98%ea%b8%b0/'
categories:
  - ejabberd
  - erlang
---
## 테스트 환경

  * ejabber node 1대, tsung node 1대, dabase node 1대
  * ulimit -n 100000

## tsung.xml

https://gist.github.com/jeidee/ae4cb98e609f405d5ce6b60a4b53f0fe