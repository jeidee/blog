---
id: 48
title: ubuntu에서 proxy를 사용한 apt-get 사용
date: 2015-01-22T00:13:28+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/22/ubuntu%ec%97%90%ec%84%9c-proxy%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%9c-apt-get-%ec%82%ac%ec%9a%a9/'
permalink: '/2015/01/22/ubuntu%ec%97%90%ec%84%9c-proxy%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%9c-apt-get-%ec%82%ac%ec%9a%a9/'
categories:
  - utility
tags:
  - proxy
  - ubuntu
---
다음과 같이 proxy 서버를 설정해 준 다음 사용 가능하다.

```
      
$ sudo vi /etc/apt/apt.conf

Acquire::http::Proxy "http://192.168.0.1:8080/";
  
```