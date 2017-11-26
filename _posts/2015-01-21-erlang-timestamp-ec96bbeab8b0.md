---
id: 14
title: erlang timestamp 얻기
date: 2015-01-21T23:30:23+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/erlang-timestamp-%ec%96%bb%ea%b8%b0/'
permalink: '/2015/01/21/erlang-timestamp-%ec%96%bb%ea%b8%b0/'
categories:
  - erlang
---
os모듈의 timestamp/0 함수를 사용한다.

```erlang
      
{MegaSecs, Secs, MicroSecs} = os:timestamp().
  
```

MegaSecs는 seconds의 (1000 * 1000)배이다.
  
위의 결과값을 milliseconds로 변환하기 위해서는 microseconds를 구해서 1000으로 나눠줘야 한다.

```erlang
      
((MegaSecs\*1000000 + Secs) \* 1000000 + MicroSecs)/1000.
  
```

소숫점 이하를 버리고자 한다면 div 연산을 사용해서 몫만 구하면 된다.

```erlang
      
((MegaSecs\*1000000 + Secs) \* 1000000 + MicroSecs) div 1000.
  
```