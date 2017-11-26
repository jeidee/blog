---
id: 12
title: erlang 문자열 포맷 후 binary로 변환
date: 2015-01-21T23:28:48+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/erlang-%eb%ac%b8%ec%9e%90%ec%97%b4-%ed%8f%ac%eb%a7%b7-%ed%9b%84-binary%eb%a1%9c-%eb%b3%80%ed%99%98/'
permalink: '/2015/01/21/erlang-%eb%ac%b8%ec%9e%90%ec%97%b4-%ed%8f%ac%eb%a7%b7-%ed%9b%84-binary%eb%a1%9c-%eb%b3%80%ed%99%98/'
categories:
  - erlang
---
c의 sprintf() 같은 문자열 포맷 함수인 io_lib:format()을 사용하면 &#8220;Count : 10&#8221; 과 같은 문자열을 binary로 만들 수 있다.

```erlang
      
Count = 20.
      
Result = io_lib:format("Count : ~p", [Count]).
      
Bin = binary:list\_to\_bin(Result).
  
```

또는 간략하게 다음과 같이 줄일 수도 있다.

```erlang
      
Bin = binary:list\_to\_bin(io_lib:format("Count : ~p", [Count]).
  
```

erlang에서 문자열이 곧 리스트이기 때문에 binary 모듈의 list\_to\_bin()함수의 매개변수로 리스트 대신 문자열을 입력해도 된다.