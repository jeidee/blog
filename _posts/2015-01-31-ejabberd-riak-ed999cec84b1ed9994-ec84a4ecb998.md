---
id: 131
title: ejabberd riak 활성화 설치
date: 2015-01-31T17:02:47+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/31/ejabberd-riak-%ed%99%9c%ec%84%b1%ed%99%94-%ec%84%a4%ec%b9%98/'
permalink: '/2015/01/31/ejabberd-riak-%ed%99%9c%ec%84%b1%ed%99%94-%ec%84%a4%ec%b9%98/'
categories:
  - ejabberd
tags:
  - riak
---
riak를 활성화하려면 다음과 같이 설치한다.

```
      
$ ./configure &#8211;enable-riak
      
$ make && make install
  
```

ejabberd 15.03 이후 릴리즈에서는 erlc 버전이 r17 이상 버전에서 정상 컴파일되는 것을 확인했다.

ejabberd 14.07 이전 릴리즈에서는 meck.erl 관련 compile에러가 날 경우 erlc 버전이 r16b3인지 확인해야 한다.

riak 모듈을 제외한 모든 플러그인 모듈을 설치하기 위해서는 다음과 같이 설치하면 된다.

```
      
$ ./configure &#8211;enable-all &#8211;disable-riak
  
```