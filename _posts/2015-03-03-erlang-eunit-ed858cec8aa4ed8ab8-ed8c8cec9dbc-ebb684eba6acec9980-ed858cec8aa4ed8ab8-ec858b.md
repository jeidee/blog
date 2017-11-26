---
id: 264
title: erlang eunit 테스트 파일 분리와 테스트 셋
date: 2015-03-03T10:37:54+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=264
permalink: '/2015/03/03/erlang-eunit-%ed%85%8c%ec%8a%a4%ed%8a%b8-%ed%8c%8c%ec%9d%bc-%eb%b6%84%eb%a6%ac%ec%99%80-%ed%85%8c%ec%8a%a4%ed%8a%b8-%ec%85%8b/'
categories:
  - erlang
tags:
  - eunit
---
## 테스트 모듈 분리

eunit을 사용할 경우 테스트 대상 모듈에 테스트 코드를 추가할 수도 있고 다음과 같이 테스트 모듈을 분리할 수도 있다.

테스트 대상 모듈을 fib.erl 이라고 가정한다.

```erlang
  
-module(fib).

-export([fib/1]).

fib(0) -> 1;
  
fib(1) -> 1;
  
fib(N) when N > 1 -> fib(N-1) + fib(N-2).
  
```

테스트 모듈은 fib_tests.erl 로 작성한다.

```erlang
  
-module(fib_tests).
  
-include_lib("eunit/include/eunit.hrl").

fib0_test() ->
      
?assert(fib:fib(0) =:= 1).

fib1_test() ->
      
?assert(fib:fib(1) =:= 1).

fib2_test() ->
      
?assert(fib:fib(2) =:= 2).

fib3_test() ->
      
?assert(fib:fib(3) =:= 3).
  
```

주목할 것은 테스트 모듈의 파일명이다.
  
테스트 대상 모듈명에 \_tests를 붙이는 것으로 끝이다.(\_test가 아니라 _tests이다)

컴파일한 후 다음과 같이 테스트를 실행할 수 있다.

```erlang
  
> eunit:test(fib).
  
```

## 배치 테스트

fib_tests.erl의 테스트 코드를 보면 4개의 테스트를 각각의 테스트함수로 생성했다.
  
대부분의 경우 여러 개의 테스트케이스를 하나의 셋으로 묶어 테스트함수를 구성하는데,
  
다음과 같이 작성하면 된다.

```erlang
  
fib\_test\_() ->
      
[?_assert(fib:fib(0) =:= 1),
       
?_assert(fib:fib(1) =:= 1),
       
?_assert(fib:fib(2) =:= 2),
       
?_assert(fib:fib(3) =:= 3),
       
?\_assertException(error, function\_clause, fib:fib(-1))
       
].
  
```

테스트 함수명을 \_test&#095; 로 끝나도록 작성하고, 테스트에 쓰일 매크로는 ?assert 대신 ?\_assert를 사용하면 된다.
  
또한 테스트케이스(매크로)는 리스트로 작성해야 한다.

또 하나 주목할 것은 ?_assertException 매크로이다.
  
fib:fib(-1)의 경우 입력값 -1에 매치하는 함수가 정의되어 있지 않으므로 다음과 같은 예외가 발생하게 된다.

```
  
** exception error: no function clause matching fib:fib(-1) (/home/gss.dev/workspace/helloerl/src/fib.erl, line 5)
  
```

이와 같은 예외가 발생했는지 확인해 주는 매크로가 바로 ?_assertException 매크로 이다.