---
id: 124
title: erlang base64 encoding
date: 2015-01-28T09:08:45+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/2015/01/28/erlang-base64-encoding/
permalink: /2015/01/28/erlang-base64-encoding/
categories:
  - erlang
tags:
  - base64
---
base64 인코딩을 위해서는 base64 모듈의 encode/1 함수를 다음과 같이 사용한다.

```erlang
      
10> Enc = base64:encode(<>).
      
<>
      
12> io:format("~s~n", [base64:decode(Enc)]).
      
encode
      
ok
  
```