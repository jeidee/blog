---
id: 257
title: erlang eunit 기초
date: 2015-02-24T18:20:09+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=257
permalink: /2015/02/24/erlang-eunit/
categories:
  - erlang
tags:
  - eunit
  - unit test
---
## eunit 기초

eunit은 최신 버전(erlang/otp 17, eshell 6.3)에 eunit-2.2.9 버전이 포함되어 있다.
  
eunit을 사용하려면 다음과 같은 순서로 진행한다.

  1. 테스트 코드 작성
  2. eunit 모듈을 사용해 테스트 수행

테스트 코드 작성하는 방법 먼저 살펴 보도록 한다.

## 테스트 코드 작성

먼저 eunit 헤더 파일을 include한다.

```erlang
  
-module(test).
  
-include_lib("eunit/include/eunit.hrl").
  
```

그런 후 다음과 같이 테스트 코드를 작성한다.

```erlang
  
reverse\_nil\_test() -> [] = lists:reverse([]).
  
reverse\_one\_test() -> [1] = lists:reverse([1]).
  
reverse\_two\_test() -> [2,1] = lists:reverse([1,2]).
  
length_test() -> ?assert(length([1,2,3]) =:= 3).
  
```

테스트 함수는 반드시 _test()로 끝나야 하며 매개변수는 없어야 한다. 이와 같이 작성하고 난 후 eunit모듈의 test/1 함수를 사용해 테스트를 수행할 수 있다.

## eunit 모듈을 사용해 테스트 수행

erlang shell을 실행한 후 test 모듈을 컴파일 한다.

```erlang
  
Eshell V6.3 (abort with ^G)
  
1> c(test).
  
{ok,test}
  
2> eunit:test(test).
    
All 4 tests passed.
  
ok
  
```

## Assert macros

eunit에는 다양한 매크로를 지원하는데 그 중 ASSERT 매크로만 살펴보도록 한다.

### assert(BoolExpr)

BoolExpr 식이 true가 아닐 경우 예외를 발생시킨다.

```erlang
  
?assert(f(X, Y) =:= [])
  
```

assert 매크로는 unit test이외에 어떤 프로그램에서도 사용될 수 있다.

```erlang
  
some\_recursive\_function(X, Y, Z) ->
      
?assert(X + Y > Z),
      
&#8230;
  
```

### assertNot(BoolExpr)

assert(not (BoolExpr))과 동일하다.

### assertMatch(GuardedPattern, Expr)

Expr의 평가 결과가 GuaredPattern과 매치하지 않을 경우 예외를 발생시킨다.

```erlang
  
?assertMatch({found, {fred, _}}, lookup(bloggs, Table))
  
?assertMatch([X|\_] when > 0, binary\_to_list(B))
  
```

### assertEqual(Expect, Expr)

Expr 평가 결과가 Expect와 동일하지 않을 경우 예외를 발생시킨다.

```erlang
  
?assertEqual("b" ++ "a", lists:reverse("ab"))
  
?assertEqual(foo(X), bar(Y))
  
```

### 그 외

assertException/3, assertError/2, assertExit/2, assertThrow/2 매크로가 있다.

## 참고

  * [Erlang E**strong text**Unit](http://www.erlang.org/doc/apps/eunit/chapter.html)