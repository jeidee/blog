---
author: jeidee
categories:
- erlang
date: '2015-01-28'
guid: https://erlnote.wordpress.com/2015/01/28/erlang-base64-encoding/
id: 124
permalink: /2015/01/28/erlang-base64-encoding/
tags:
- base64
title: erlang base64 encoding
url: /2015/01/28/erlang-base64-encoding
---

base64 인코딩을 위해서는 base64 모듈의 encode/1 함수를 다음과 같이 사용한다.

```erlang
      
10> Enc = base64:encode(<>).
      
<>
      
12> io:format("~s~n", [base64:decode(Enc)]).
      
encode
      
ok
  
```