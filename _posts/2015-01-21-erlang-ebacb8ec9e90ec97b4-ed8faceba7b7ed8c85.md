---
author: jeidee
categories:
- erlang
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/erlang-%eb%ac%b8%ec%9e%90%ec%97%b4-%ed%8f%ac%eb%a7%b7%ed%8c%85/
id: 13
permalink: /2015/01/21/erlang-%eb%ac%b8%ec%9e%90%ec%97%b4-%ed%8f%ac%eb%a7%b7%ed%8c%85/
title: erlang 문자열 포맷팅
url: /2015/01/21/erlang-ebacb8ec9e90ec97b4-ed8faceba7b7ed8c85
---

문자열 포맷하기 위해서는 io_lib:format/2 함수를 사용하면 된다.
  
하지만 다음과 같이 바로 사용할 수 없는 형태이다.

```erlang
      
17> Str = io_lib:format("name=~p&age=~p", ["romeo", 10]).
      
[110,97,109,101,61,"\"romeo\"",38,97,103,101,61,"10"]
      
18> Str.
      
[110,97,109,101,61,"\"romeo\"",38,97,103,101,61,"10"]
      
19>
  
```

이럴 경우 lists모듈의 flatten/1 함수를 사용한다.

```erlang
      
19> Result = lists:flatten(Str).
  
"name=\"romeo\"&age=10"
  
```

한 줄로 줄여서 다음과 같이 사용해도 된다.

```erlang
      
20> lists:flatten(io_lib:format("name=~p&age=~p", ["romeo", 10])).
      
"name=\"romeo\"&age=10"
  
```