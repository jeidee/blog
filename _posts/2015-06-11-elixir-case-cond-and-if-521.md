---
id: 445
title: 'Elixir case, cond and if (#5/21)'
date: 2015-06-11T14:32:06+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=445
permalink: /2015/06/11/elixir-case-cond-and-if-521/
geo_public:
  - "0"
categories:
  - elixir
tags:
  - tutorial
---
> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

이번 챕터에서는 흐름 제어에 사용되는 case, cond, if에 대해 알아본다.

## case

case는 매치되는 하나를 찾기 위해 많은 패턴과 값을 비교할 때 사용한다.

```
  
iex> case {1, 2, 3} do
  
&#8230;> {4, 5, 6} ->
  
&#8230;> "This clause won't match"
  
&#8230;> {1, x, 3} ->
  
&#8230;> "This clause will match and bind x to 2 in this clause"
  
&#8230;> _ ->
  
&#8230;> "This clause would match any value"
  
&#8230;> end
  
"This clause will match and bind x to 2 in this clause"
  
```

기존의 변수에 패턴매치를 사용하고 싶다면 핀 연산자(^)를 사용해야 한다.

```
  
iex> x = 1
  
1
  
iex> case 10 do
  
&#8230;> ^x -> "Won't match"
  
&#8230;> _ -> "Will match"
  
&#8230;> end
  
"Will match"
  
```

가드(guards)를 사용해서 추가 조건식을 사용할 수 있다.

```
  
iex> case {1, 2, 3} do
  
&#8230;> {1, x, 3} when x > 0 ->
  
&#8230;> "Will match"
  
&#8230;> _ ->
  
&#8230;> "Won't match"
  
&#8230;> end
  
"Will match"
  
```

(가드는 매치를 먼저 수행한후 when 뒤에 추가 조건식을 작성하는 것을 말한다.)

## 가드 절(guard clauses)에서의 표현식

Erlang VM은 가드에서 사용할 표현식을 다음과 같이 제한하고 있다.

  * 비교 연산자(==, !=, ===, !==, <, >, <=, >=)
  * boolean 연산자(and, or)와 부정 연산자(not, !)
  * 산술 연산자(+, -, *, /)
  * 좌측 값으로 사용되는 &lgt;>와 ++
  * in 연산자
  * 다음의 타입 검사 함수들 
      * is_atom/1
      * is_binary/1
      * is_bitstring/1
      * is_boolean/1
      * is_float/1
      * is_function/1
      * is_function/2
      * is_integer/1
      * is_list/1
      * is_map/1
      * is_nil/1
      * is_number/1
      * is_pid/1
      * is_port/1
      * is_reference/1
      * is_tuple/1
  * 다음 함수들도 가능하다. 
      * abs(number)
      * bit_size(bitstring)
      * byte_size(bitstring)
      * div(integer, integer)
      * elem(tuple, n)
      * hd(list)
      * length(list)
      * map_size(map)
      * node()
      * node(pid | ref | port)
      * rem(integer, integer)
      * round(number)
      * self()
      * tl(list)
      * trunc(number)
      * tuple_size(tuple)

유저가 자신의 가드 함수를 작성한다면 일반적으로 &#8220;is_&#8221;로 시작하는 이름을 사용하도록 한다.

가드 안에서 에러가 발생하면 예외가 발생하지 않고 가드가 실패할 뿐이다.

```
  
iex> hd(1)
  
** (ArgumentError) argument error
      
:erlang.hd(1)
  
iex> case 1 do
  
&#8230;> x when hd(x) -> "Won't match"
  
&#8230;> x -> "Got: #{x}"
  
&#8230;> end
  
"Got 1"
  
```

매치하는 절이 없을 경우 에러가 발생한다.(case 절에는 반드시 매치하는 절이 포함되어야 한다.)

```
  
iex> case :ok do
  
&#8230;> :error -> "Won't match"
  
&#8230;> end
  
** (CaseClauseError) no case clause matching: :ok
  
```

익명 함수 역시 가드를 사용해 다음과 같이 작성할 수 있다.

```
  
iex> f = fn
  
&#8230;> x, y when x > 0 -> x + y
  
&#8230;> x, y -> x * y
  
&#8230;> end
  
#Function<12.71889879/2 in :erl_eval.expr/5>
  
iex> f.(1, 3)
  
4
  
iex> f.(-1, 3)
  
-3
  
```

익명함수의 매개변수 개수는 각 절(clause)마다 동일해야하고, 그렇지 않을 경우 에러가 발생한다.

## cond

case의 경우 특정 값 기준으로 정확히 매치되는 절을 수행하는데 유용하지만, 많은 경우 여러 조건 중에 첫 번째로 true인 경우(뒤 이은 조건들 중에 true로 평가되는 경우가 있다 하더라도)만 찾기를 원하는 경우가 있다.
  
이럴 경우 case 대신 다음과 같이 cond를 사용한다.

```
  
iex> cond do
  
&#8230;> 2 + 2 == 5 ->
  
&#8230;> "This will not be true"
  
&#8230;> 2 * 2 == 3 ->
  
&#8230;> "Nor this"
  
&#8230;> 1 + 1 == 2 ->
  
&#8230;> "But this will"
  
&#8230;> end
  
"But this will"
  
```

cond는 일반적인 언어에 있는 else if 구문과 동일하다.

cond의 조건식 중 true인 경우가 없다면 에러가 발생한다. 이런 상황을 방지하려면 항상 마지막 조건으로 true ->를 사용해야 한다.

```
  
iex> cond do
  
&#8230;> 2 + 2 == 5 ->
  
&#8230;> "This is never true"
  
&#8230;> 2 * 2 == 3 ->
  
&#8230;> "Nor this"
  
&#8230;> true ->
  
&#8230;> "This is always true (equivalent to else)"
  
&#8230;> end
  
"This is always true (equivalent to else)"
  
```

cond의 조건식은 nil이나 false가 아닌 경우 모두 true로 평가함을 주의하자.

```
  
iex> cond do
  
&#8230;> hd([1,2,3]) ->
  
&#8230;> "1 is considered as true"
  
&#8230;> end
  
"1 is considered as true"
  
```

## if와 unless

elixir는 case와 cond이외에 단 하나의 조건을 평가하기 위한 용도로 if/2와 unless/2 매크로를 제공한다.

```
  
iex> if true do
  
&#8230;> "This works!"
  
&#8230;> end
  
"This works!"
  
iex> unless true do
  
&#8230;> "This will never be seen"
  
&#8230;> end
  
nil
  
```

if/2 매크로에 주어진 조건식이 false나 nil을 반환하면, body에 해당하는 do/end 블럭의 코드는 실행되지 않고, nil을 리턴한다. (unless/2는 반대)

if/2와 unless/2는 else 블록을 지원한다.

```
  
iex> if nil do
  
&#8230;> "This won't be seen"
  
&#8230;> else
  
&#8230;> "This will"
  
&#8230;> end
  
"This will"
  
```

> if/2와 unless/2는 매크로임을 주목하자. kernel 모듈에서 if/2 소스를 확인할 수 있다. kernel 모듈에는 +/2 연산자와 is_function/2 함수도 정의되어 있고, 별도의 import과정없이 기본적으로 모든 코드에서 사용할 수 있다. 

## do/end 블럭

이 시점에서, case, cond, if, unless는 do/end 블럭을 갖는다는 사실을 알게되었을 것이다. 하지만 do/end 블럭 없이 다음과 같이 작성할 수도 있다.

```
  
iex> if true, do: 1 + 2
  
3
  
```

elixir에서 do/end 블럭은 표현식의 그룹(여러 표현식들)을 do:에 편하게 전달하게 도와준다. 다음은 동등한 구문이다.

```
  
iex> if true do
  
&#8230;> a = 1 + 2
  
&#8230;> a + 10
  
&#8230;> end
  
13
  
iex> if true, do: (
  
&#8230;> a = 1 + 2
  
&#8230;> a + 10
  
&#8230;> )
  
13
  
```

두 번째 구문은 키워드 리스트(do/end 블럭 대신 소괄호를 사용)를 사용한다고 말할 수 있다. 다음 구문을 사용해서 else 절을 실행할 수 있다.

```
  
iex> if false, do: :this, else: :that
  
:that
  
```

do/end 블럭을 사용할 때 다음의 경우를 주의해야 한다.

```
  
iex> is_number if true do
  
&#8230;> 1 + 2
  
&#8230;> end
  
** (RuntimeError) undefined function: if/1
  
```

위의 구문은 다음과 같이 해석된다.

```
  
iex> is_number(if true) do
  
&#8230;> 1 + 2
  
&#8230;> end
  
** (RuntimeError) undefined function: if/1
  
```

즉, 의도한 바가 is_number/1의 매개변수로 if 절의 do/end 블럭의 값이 되기를 기대하지만, 위와 같이 작성해서는 원하는 결과를 얻을 수 없고 대신 다음과 같이 작성해야 한다.

```
  
iex> is_number(if true do
  
&#8230;> 1 + 2
  
&#8230;> end)
  
true
  
```

키워드 리스트를 사용해서 모호한 표현(잘못 해석될 수 있는)의 가능성을 제거할 수 있다.
  
키워드 리스트는 언어에서 중요한 역할을 수행하고, 많은 함수와 매크로에서 매우 일반적이으로 사용된다. 키워드 리스트에 대해서는 다른 챕터에서 더 자세히 살펴볼 기회가 있을 것이다.

## 참고

  * [Elixir case, cond and if](http://elixir-lang.org/getting-started/case-cond-and-if.html)