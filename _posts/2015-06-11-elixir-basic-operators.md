---
author: jeidee
categories:
- elixir
date: '2015-06-11'
guid: https://erlnote.wordpress.com/?p=434
id: 434
permalink: /2015/06/11/elixir-basic-operators/
tags:
- tutorial
title: Elixir Basic operators (#3/21)
url: /2015/06/11/elixir-basic-operators
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

이전 챕터에서는 +, -, *, / 산술 연산자를 살펴보았다.
  
이 외에도 elixir는 리스트 연산을 위해 ++와 &#8212; 연산자를 제공한다.

```
  
iex> [1,2,3] ++ [4,5,6]
  
[1,2,3,4,5,6]
  
iex> [1,2,3] &#8212; [2]
  
[1,3]
  
```

문자열의 결합에는 연산자를 사용한다.

```
  
iex> "foo" <> "bar"
  
"foobar"
  
```

elixir는 논리 연산을 위해 or, and, not과 같은 논리 연산자를 제공한다. 연산의 결과는 true 또는 false값이며 연산자의 매개변수 역신 boolean타입(true or false)이어야 한다.

```
  
iex> true and true
  
true
  
iex> false or is_atom(:example)
  
true
  
```

논리 연산자의 매개변수로 boolean타입이 제공되지 않으면 예외를 발생시킨다.

```
  
iex> 1 and true
  
** (ArgumentError) argument error
  
```

or 와 and 연산자는 short-circuit 연산자이다. short-circuit 연산은 좌측 연산의 결과에 따라 우측 연산의 수행을 결정(무시할지 말지)하는 연산을 말한다.

```
  
iex> false and error("This error will never be raised")
  
false

iex> true or error("This error will never be raised")
  
true
  
```

> erlang에서는 elixir의 and와 or 연산자는 andalso와 orelse연산자와 동일하다. 

또한 ||, &&, ! 연산자도 제공하는데 이 연산자의 경우 어떤 타입도 매개변수로 올 수 있다. 이들 연산자에 사용되는 데이터 타입의 값은 false와 nil을 제외하고 모두 true로 평가된다.

```
  
\# or
  
iex> 1 || true
  
1
  
iex> false || 11
  
11

\# and
  
iex> nil && 13
  
nil
  
iex> true && 17
  
17

\# !
  
iex> !true
  
false
  
iex> !1
  
false
  
iex> !nil
  
true
  
```

||와 && 연산의 결과sms true나 false가 아닌 short-circuit 규칙에 따른 결과가 출력됨을 유의해야 한다.

elixir는 비교 연산을 위해 ==, !=, ===, !==, =, 연산자도 제공한다.

```
  
iex> 1 == 1
  
true
  
iex> 1 != 2
  
true
  
iex> 1 < 2
  
true
  
```

==와 ===연산자의 차이는 === 연산자가 integer와 float 연산에서 좀 더 정확한 연산을 수행한다는 것이다.

```
  
iex> 1 == 1.0
  
true
  
iex> 1 === 1.0
  
false
  
```

elixir에서는 서로 다른 타입의 비교 연산도 가능하다.

```
  
iex> 1 < :atom
  
true
  
```

이것이 가능한 이유는 서로 다른 데이터타입의 비교가 실용적이기 때문이다. 정렬 알고리즘을 사용할 때 데이터타입이 다르다고 해서 걱정할 필요가 없다. 타입별 정렬 우선순위는 다음과 같다.

```
  
number < atom < reference < functions < port < pid < tuple < maps < list < bitstring
  
```

위의 정렬순서를 기억할 필요는 없지만, 정렬 우선순위가 존재한다는 것은 기억하고 있어야 한다.

## 참고

  * [Elixir Basic Operators](http://elixir-lang.org/getting-started/basic-operators.html)