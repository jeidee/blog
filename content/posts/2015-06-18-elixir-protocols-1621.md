---
author: jeidee
categories:
- elixir
date: '2015-06-18'
guid: https://erlnote.wordpress.com/?p=501
id: 501
permalink: /2015/06/18/elixir-protocols-1621/
tags:
- tutorial
title: Elixir Protocols (#16/21)
url: /2015/06/18/elixir-protocols-1621
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

프로토콜은 elixir에서 다형성을 구현하는 매커니즘이다. 프로토콜은 어떤 데이터 타입에서도 사용할 수 있다. 예제를 보도록 하자.

elixir에서는 false와 nil만이 false처럼 처리된다. 그 외에는 모든 것이 true로 평가된다. 응용프로그램에 따라, blank로 처리되는 데이터타입을 위해 boolean값을 반환하는 blank? 프로토콜을 명시하는 것이 중요할 수도 있다. 예를 들어, 빈 리스트나 빈 바이너리는 blank로 간주될 수 있다.

우리는 이러한 프로토콜을 다음과 같이 정의할 수 있다.

```
  
defprotocol Blank do
    
@doc "Returns true if data is considered blank/empty"
    
def blank?(data)
  
end
  
```

이 프로토콜은 하나의 매개변수를 갖도록 구현된 blank? 함수가 호출되기를 기대한다. 우리는 다음과 같이 다른 데이터타입에 이 프로토콜을 구현할 수 있다.

```
  
\# Integers are never blank
  
defimpl Blank, for: Integer do
    
def blank?(_), do: false
  
end

\# Just empty list is blank
  
defimpl Blank, for: List do
    
def blank?([]), do: true
    
def blank?(_), do: false
  
end

\# Just empty map is blank
  
defimpl Blank, for: Map do
    
\# Keep in mind we could not pattern match on %{} because
    
\# it matches on all maps. We can however check if the size
    
\# is zero (and size is a fast operation).
    
def blank?(map), do: map_size(map) == 0
  
end

\# Just the atoms false and nil are blank
  
defimpl Blank, for: Atom do
    
def blank?(false), do: true
    
def blank?(nil), do: true
    
def blank?(_), do: false
  
end
  
```

> 역주: elixir의 프로토콜은 c#/java의 interface나 또는 objective-c의 protocol보다는 objective-c의 category와 조금 더 가까운 느낌이다. objective-c의 category는 기존 타입을 확장(함수 추가)하는 역할을 한다. 

그리고 우리는 모든 네이티브 데이터 타입에 위와 같이 할 수 있다. 가능한 타입은 다음과 같다.

  * Atom
  * BitString
  * Float
  * Function
  * Integer
  * List
  * Map
  * PID
  * Port
  * Reference
  * Tuple

이제 프로토콜을 정의했고 데이터타입에 구현했으므로, 다음과 같이 사용할 수 있다.

```
  
iex> Blank.blank?(0)
  
false
  
iex> Blank.blank?([])
  
true
  
iex> Blank.blank?([1, 2, 3])
  
false
  
```

구현되지 않은 데이터 타입을 넘기면 에러가 발생한다.

```
  
iex> Blank.blank?("hello")
  
** (Protocol.UndefinedError) protocol Blank not implemented for "hello"
  
```

## Protocols and structs

elixir 확장의 힘은 프로토콜과 구조체를 함께 사용할 때 느낄 수 있다.

이전 챕터에서, 우리는 구조체가 맵이긴 하지만, 맵에 구현된 프로토콜을 공유하지는 않는다고 배웠다. 이전 챕터에서 살펴본 User 구조체를 보자.

```
  
iex> defmodule User do
  
&#8230;> defstruct name: "john", age: 27
  
&#8230;> end
  
{:module, User,
   
<<70, 79, 82, &#8230;>>, {:\_\_struct\_\_, 0}}
  
```

그리고 다음과 같이 검사해 보자.

```
  
iex> Blank.blank?(%{})
  
true
  
iex> Blank.blank?(%User{})
  
** (Protocol.UndefinedError) protocol Blank not implemented for %User{age: 27, name: "john"}
  
```

맵과 프로토콜 구현을 공유하는 대신, 구조체는 User만의 프로토콜 구현을 요구한다.

```
  
defimpl Blank, for: User do
    
def blank?(_), do: false
  
end
  
```

원한다면, 우리 자신의 User 구조체를 위한 Blank 프로토콜을 정의할 수 있다. 그 뿐아니라, 큐와 같은 좀 더 강건한 데이터타입을 만들기 위해 구조체를 사용할 수 있는데, Enumarable에 Blank를 구현하는 것과 같이 이러한 데이터타입에도 프로토콜을 구현할 수 있다.

많은 경우에, 모든 구조체에 프토토콜을 명시적으로 구현하는 것이 지루해질 수 있기 때문에, 개발자는 구조체를 위한 기본 구현을 제공하길 원할지도 모르겠다. 이런 경우에 @fallback\_to\_any를 사용할 수 있다.

## Falling back to Any

모든 타입에 기본 구현을 제공하는 편리한 방법이 있다. 프로토콜 정의에 @fallback\_to\_any를 true로 설정하면된다.

```
  
defprotocol Blank do
    
@fallback\_to\_any true
    
def blank?(data)
  
end
  
```

이 프로토콜은 다음과 같이 구현할 수 있다.

```
  
defimpl Blank, for: Any do
    
def blank?(_), do: false
  
end
  
```

이제 모든 데이터 타입(구조체를 포함해서)에서 blank는 false 값을 갖는다.

## Built-in protocols

elixir는 약간의 내장 프로토콜을 보유하고 있다. 이전 챕터에서, 우리는 Enumerable 프로토콜을 구현해서 어떤 데이터 구조라도 처리할 수 있는 많은 함수를 제공하는 Enum 모듈을 배웠다.

```
  
iex> Enum.map [1, 2, 3], fn(x) -> x * 2 end
  
[2,4,6]
  
iex> Enum.reduce 1..3, 0, fn(x, acc) -> x + acc end
  
6
  
```

다른 유용한 예로, String.Chars 프로토콜이 있는데, 문자로 구성된 데이터 구조를 문자열로 변경하는데 유용하게 쓰인다. 해당 프로토콜에는 to_string 함수를 노출하고 있다.

```
  
iex> to_string :hello
  
"hello"
  
```

elixir에서 문자열 삽입은 to_string 함수를 호출한다는 것을 명심하자.

```
  
iex> "age: #{25}"
  
"age: 25"
  
```

위의 코드 조각은 숫자(numbers)가 String.Chars 프로토콜을 구현하고 있기 때문에 작동한다. 튜플을 넘겨주면 오류가 발생할 것이다.

```
  
iex> tuple = {1, 2, 3}
  
{1, 2, 3}
  
iex> "tuple: #{tuple}"
  
** (Protocol.UndefinedError) protocol String.Chars not implemented for {1, 2, 3}
  
```

Inspect 프로토콜에 기반한 inspect 함수를 사용할 경우, 좀 더 복잡한 데이터 구조를 출력할 수 있다.

```
  
iex> "tuple: #{inspect tuple}"
  
"tuple: {1, 2, 3}"
  
```

Inspect 프로토콜은 어떤 데이터 구조라도 읽을 수 있는 텍스트 형태의 표현식으로 변경하는데 사용하는 프로토콜이다. 이것은 IEx같은 툴이 결과를 출력하는데 사용된다.

```
  
iex> {1, 2, 3}
  
{1,2,3}
  
iex> %User{}
  
%User{name: "john", age: 27}
  
```

명심할 것은, 관습에의해, #으로 시작하는 값을 검사할 때는 항상, 유효하지 않은 elixir 구문으로 데이터 구조를 표현한다는 것이다. 이것이 의미하는 바는, 정보의 손실이 있을 수 있기 때문에,Inspect 프로토콜이 역으로 변환되지 않는 다는 것이다.

```
  
iex> inspect &(&1+2)
  
"#Function<6.71889879/1 in :erl_eval.expr/5>"
  
```

## 참고

  * [Elixir Protocols](http://elixir-lang.org/getting-started/protocols.html)