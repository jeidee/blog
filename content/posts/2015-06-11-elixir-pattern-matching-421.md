---
author: jeidee
categories:
- elixir
date: '2015-06-11'
guid: https://erlnote.wordpress.com/?p=438
id: 438
permalink: /2015/06/11/elixir-pattern-matching-421/
tags:
- tutorial
title: Elixir Pattern matching (#4/21)
url: /2015/06/11/elixir-pattern-matching-421
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

이번 챕터에서는 = 연산자가 매치 연산자로 어떻게 사용되는지와 자료구조안에서 패턴매치가 어떻게 이뤄지는지 살펴볼 것이다. 마지막으로 핀 연산자(^)를 알아본다.

## 매치 연산자

elixir에서 변수에 값을 할당하기 위해 = 연산자를 여러번 사용했다.

```
  
iex> x = 1
  
1
  
iex> x
  
1
  
```

c/c++에서 = 연산자는 대입연산자(또는 할당연산자)라고 불리우지만 erlang과 elixir에서는 매치 연산자라고 부른다.
  
왜 그런지 살펴보자.

```
  
iex> 1 = x
  
1
  
iex> 2 = x
  
** (MatchError) no match of right hand side value: 1
  
```

1 = x 표현식이 유효함을 주목한다. 현재 x에는 1이란 값이 할당되어 있고 좌측의 값(1)과 우측의 값(x)이 일치하기 때문에 예외가 발생하지 않는다.
  
2 = x 표현식은 좌/우측의 값이 매치하지 않으므로 MatchError 예외가 발생한다.

변수는 오직 = 연산자의 좌측에서만 값을 할당받을 수 있다.

```
  
iex> 1 = unknown
  
** (RuntimeError) undefined function: unknown/0
  
```

RuntimeError가 발생하는 이유는 unknown 변수가 이전에 정의(변수에 값이 할당되어 있지 않다)되어 있지 않기 때문이며, elixir는 변수가 아니라 함수호출이 아닐까 추측하고 unknown/0함수가 정의되어 있지 않다는 예외를 발생시킨다.

> erlang에서 변수는 한 번만 할당할 수 있지만, elixir에서는 동일 변수에 다른 값을 여러번 할당할 수 있다. 

## 패턴 매칭

매치 연산자는 단순한 값을 매치할 때만 사용하지 않고 좀 더 복잡한 데이터타입의 구조를 매치할 때도 사용할 수 있다. 예를 들면 튜플에서 다음과 같이 사용할 수 있다.

```
  
iex> {a, b, c} = {:hello, "world", 42}
  
{:hello, "world", 42}
  
iex> a
  
:hello
  
iex> b
  
"world"
  
```

a, b, c 변수에 동일한 튜플구조의 우측값을 사용해 각각 :hello, &#8220;world&#8221;, 42를 한 번에 매치시키는 예제이다.

만약 구조가 동일하지 않을 경우 다음과 같이 MatchError가 발생될 것이다.

```
  
iex> {a, b, c} = {:hello, "world"}
  
** (MatchError) no match of right hand side value: {:hello, "world"}
  
```

또한 데이터타입이 상이할 때도 예외가 발생한다.

```
  
iex> {a, b, c} = [:hello, "world", "!"]
  
** (MatchError) no match of right hand side value: [:hello, "world", "!"]
  
```

조금 더 흥미로운 것은 특정값을 사용해 매치를 시킬 수 있다는 것이다. 다음 예를 보면 첫 번째 요소가 :ok인 튜플과 매치할 때에만 유효한 평가식을 살펴볼 수 있다.

```
  
iex> {:ok, result} = {:ok, 13}
  
{:ok, 13}
  
iex> result
  
13

iex> {:ok, result} = {:error, :oops}
  
** (MatchError) no match of right hand side value: {:error, :oops}
  
```

리스트를 대상으로 패턴매치를 수행할 수 있다.

```
  
iex> [a, b, c] = [1, 2, 3]
  
[1, 2, 3]
  
iex> a
  
1
  
```

리스트의 경우에는 head와 tail을 나누어 다음과 같이 매치를 활용할 수도 있다.

```
  
iex> [head | tail] = [1, 2, 3]
  
[1, 2, 3]
  
iex> head
  
1
  
iex> tail
  
[2, 3]
  
```

리스트의 head는 첫번째 요소의 값이고, tail은 head를 제외한 나머지 요소를 갖는 리스트를 의미한다.
  
hd/1, tl/1함수를 사용하는 것과 유사한 결과를 패턴매칭을 사용해 구현할 수 있다.

```
  
iex> [h|t] = []
  
** (MatchError) no match of right hand side value: []
  
```

[head | tail] 형태는 패턴 매치뿐만 아니라 리스트의 선두에 아이템을 삽입(prepending item)하는데도 사용할 수 있다.

```
  
iex> list = [1, 2, 3]
  
[1, 2, 3]
  
iex> [0|list]
  
[0, 1, 2, 3]
  
```

패턴 매칭은 튜플과 리스트 같은 데이터타입의 구조를 해석(destructure)하는것을 용이하게 해 준다. 다음 챕터에서 elixir의 재귀 함수 중의 하나를 살펴보며, map이나 binary같은 타입에 어떻게 적용되는지 살펴보게 될 것이다.

## 핀 연산자

elixir에서 변수는 재할당(rebound)될 수 있다.

```
  
iex> x = 1
  
1
  
iex> x = 2
  
2
  
```

핀 연산자(^)는 변수가 재할당되지 않도록 방지하며, 오직 값이 매치하는지에만 사용한다.

```
  
iex> x = 1
  
1
  
iex> ^x = 2
  
** (MatchError) no match of right hand side value: 2
  
iex> {x, ^x} = {2, 1}
  
{2, 1}
  
iex> x
  
2
  
```

x에 새로운 값인 2를 재할당(rebound)하기 위해 이전 x(^x)의 값이 1과 매치하는지 확인하고 있다.
  
아래와 같이 패턴에서 변수가 한 번 이상 언급(사용)될 경우, 모든 참조는 동일한 패턴에 바인드되어야 한다.
  
(역주 &#8211; 값의 할당이 일어나지 않고, 값의 패턴 매칭만 수행하게 된다는 의미 같다.)

```
  
iex> {x, x} = {1, 1}
  
1
  
iex> {x, x} = {1, 2}
  
** (MatchError) no match of right hand side value: {1, 2}
  
```

패턴에서 특정한 값을 무시(매치할 필요도 없고 변수에 바인드할 필요도 없는)해야할 경우가 있는데, underscore(_)의 경우 해당 용도로 다음과 같이 사용할 수 있다.

```
  
iex> [h | _] = [1, 2, 3]
  
[1, 2, 3]
  
iex> h
  
1
  
```

리스트의 tail을 \_에 바인드했지만 사실상 무시한 것과 동일하며 \_는 읽을 수 없다.

```
  
iex> _
  
** (CompileError) iex:1: unbound variable _
  
```

패턴 매칭이 강력한 구조를 만들도록 해 주지만, 사용이 제한적이다. 일례로, 좌측에 함수를 사용하여 매치할 수 없다.

```
  
iex> length([1,[2],3]) = 3
  
** (CompileError) iex:1: illegal pattern
  
```

## 참고

  * [Elixir Pattern matching](http://elixir-lang.org/getting-started/pattern-matching.html)