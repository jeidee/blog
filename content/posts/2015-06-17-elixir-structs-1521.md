---
author: jeidee
categories:
- elixir
date: '2015-06-17'
guid: https://erlnote.wordpress.com/?p=499
id: 499
permalink: /2015/06/17/elixir-structs-1521/
tags:
- tutorial
title: Elixir Structs (#15/21)
url: /2015/06/17/elixir-structs-1521
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

이전 챕터에서 우리는 맵에 대해 배웠다.

```
  
iex> map = %{a: 1, b: 2}
  
%{a: 1, b: 2}
  
iex> map[:a]
  
1
  
iex> %{map | a: 3}
  
%{a: 3, b: 2}
  
```

구조체는 컴파일 타임 검사와 기본값을 제공하는 맵의 확장이다.

## Defining structs

구조체를 정의하기 위해, defstruct를 사용한다.

```
  
iex> defmodule User do
  
&#8230;> defstruct name: "John", age: 27
  
&#8230;> end
  
```

defstruct에 사용된 키워드 리스트는, 어떤 필드가 어떤 기본값을 갖는지 정의한다.

구조체는 구조체가 정의된 모듈의 이름을 통해서 가져온다. 위의 예에는 User 모듈에 구조체를 정의했다.

우리가 만든 User 구조체는 맵과 유사한 구문으로 사용할 수 있다.

```
  
iex> %User{}
  
%User{age: 27, name: "John"}
  
iex> %User{name: "Meg"}
  
%User{age: 27, name: "Meg"}
  
```

구조체는 컴파일타임에 defstruct를 통해 정의된 필드만 사용할 수 있음을 보장한다.

```
  
iex> %User{oops: :field}
  
** (CompileError) iex:3: unknown key :oops for struct User
  
```

## Accessing and updating structs

우리가 맵을 배울때, 맵의 필드에 어떻게 접근하고 값을 수정할 수 있는지 보여주었다. 같은 테크닉(같은 구문)으로 구조체를 사용할 수 있다.

```
  
iex> john = %User{}
  
%User{age: 27, name: "John"}
  
iex> john.name
  
"John"
  
iex> meg = %{john | name: "Meg"}
  
%User{age: 27, name: "Meg"}
  
iex> %{meg | oops: :field}
  
** (ArgumentError) argument error
  
```

수정 구문(|)을 사용할 때, VM은 메모리에 저장된 공유 구조체 하부가 맵이기 때문에, 어떤 새로운 키도 추가되지 않을 것임을 알고 있다. 위의 예에서, john과 meg은 메모리에서 동일한 키를 공유하고 있다.

구조체는 패턴 매칭에도 사용될 수 있다.

```
  
iex> %User{name: name} = john
  
%User{age: 27, name: "John"}
  
iex> name
  
"John"
  
iex> %User{} = %{}
  
** (MatchError) no match of right hand side value: %{}
  
```

## Structs are just bare maps underneath(구조체는 사실 순수한 맵이다.)

위의 예에서, 패턴매칭이 동작하는 이유는 하부 구조가 단순히 맵이기 때문이다. 맵 처럼, 구조체도 **struct**라는 &#8220;특별한&#8221; 필드를 저장하는데, 구조체의 이름을 보유하고 있다.

```
  
iex> is_map(john)
  
true
  
iex> john.\_\_struct\_\_
  
User
  
```

우리는 맵을 위해 구현된 어떤 프로토콜도 구조체에서는 사용할 수 없기 때문에, 순수한 맵처럼 취급해야 함을 명심하자. 예를 들어, 구조체 내부를 접근해서 열거할 수 없다.

```
  
iex> john = %User{}
  
%User{age: 27, name: "John"}
  
iex> john[:name]
  
** (Protocol.UndefinedError) protocol Access not implemented for %User{age: 27, name: "John"}
  
iex> Enum.each john, fn({field, value}) -> IO.puts(value) end
  
** (Protocol.UndefinedError) protocol Enumerable not implemented for %User{age: 27, name: "John"}
  
```

구조체는 사전(dictionary) 역시 아니므로 Dict 모듈의 함수에서 사용할 수 없다.

```
  
iex> Dict.get(%User{}, :name)
  
** (UndefinedFunctionError) undefined function: User.fetch/2
  
```

하지만, 구조체는 맵이기 때문에, Map 모듈의 함수에서는 사용할 수 있다.

```
  
iex> kurt = Map.put(%User{}, :name, "Kurt")
  
%User{age: 27, name: "Kurt"}
  
iex> Map.merge(kurt, %User{name: "Takashi"})
  
%User{age: 27, name: "Takashi"}
  
iex> Map.keys(john)
  
[:\_\_struct\_\_, :age, :name]
  
```

## 참고

  * [Elixir Structs](http://elixir-lang.org/getting-started/structs.html)