---
author: jeidee
categories:
- elixir
date: '2015-06-12'
guid: https://erlnote.wordpress.com/?p=451
id: 451
permalink: /2015/06/12/elixir-keywords-maps-and-dicts-721/
tags:
- tutorial
title: Elixir Keywords, maps and dicts (#7/21)
url: /2015/06/12/elixir-keywords-maps-and-dicts-721
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

지금까지 자료구조와 관련된 논의는 하지 않았다. 예를 들어, 키에 특정 값을 연관시키는 자료구조 같은 것 등을 말이다. 다른 언어에서는 이러한 자료구조를 사전(Dictionary), 해시(Hash), 연관 배열(Associative Array), 맵(Map)등으로 부른다.

elixir에는 두 개의 주요 연관 자료구조가 있다: 키워드 리스트(keyword lists)와 맵(maps)이다. 이번에는 이들에 대해 배워보도록 한다.

## Keyword lists

많은 함수형 프로그래밍 언어에서는, 연관 자료구조를 표현하는데 tuple 두 개를 사용한 리스트를 일반적으로 사용한다. elixir에서는 첫 번째 요소가 atom이며 두 번째 요소가 값인 튜플을 갖는 리스트를 사용할 때, 이를 키워드 리스트라고 부른다.

```
  
iex> list = [{:a, 1}, {:b, 2}]
  
[a: 1, b: 2]
  
iex> list == [a: 1, b: 2]
  
true
  
iex> list[:a]
  
1
  
```

보다시피, elixir는 이러한 리스트를 정의하기 위한 특수한 구문을 가지고 있으며, 내부적으로 튜플의 리스트로 이뤄져 있다. 이들은 단순히 리스트이기때문에, 리스트와 관련된 모든 처리를 수행할 수 있고, 리스트의 성능 관련 특성이 키워드 리스트에도 그대로 적용된다.

예를 들어, 키워드 리스트에 ++ 연산자를 사용해 새로운 값을 추가할 수 있다.

```
  
iex> list ++ [c: 3]
  
[a: 1, b: 2, c: 3]
  
iex> [a: 0] ++ list
  
[a: 0, a: 1, b: 2]
  
```

주목할 것은 두 번째 문장의 경우 리스트의 앞에 중복 추가되며(이미 a: 1 요소가 있지만), 조회를 하게 되면 첫 번째 요소(새로 추가된 a: 0)만 가져오게 된다.

```
  
iex> new_list = [a: 0] ++ list
  
[a: 0, a: 1, b: 2]
  
iex> new_list[:a]
  

  
```

키워드 리스트는 다음의 세가지 특성때문에 매우 중요하다.

  * 키는 반드시 atom이어야 한다.
  * 키는 개발자가 지정한대로 정렬되어 있다.
  * 키는 한 번 이상 주어질 수 있다.

예를 들어, [Ecto library](https://github.com/elixir-lang/ecto)의 경우 이러한 특징을 데이터베이스 쿼리를 우아하게 작성하는데 사용하고 있다.

```
  
query = from w in Weather,
        
where: w.prcp > 0,
        
where: w.temp < 20,
       
select: w
  
```

이러한 특징들로 인해 elixir에서는 함수에 옵션을 넘겨줄 때 사용하는 기본 매커니즘으로 키워드 리스트를 사용하고 있다. 챕터 5에서, if/2 매크로를 살펴보았는데, 다음 구문이 지원됨을 언급했었다.

```
  
iex> if false, do: :this, else: :that
  
:that
  
```

do:와 else: 쌍은 키워드 리스트이다! 사실, 위의 구문은 다음과 동일하다.

```
  
iex> if(false, [do: :this, else: :that])
  
:that
  
```

일반적으로, 키워드리스트가 함수의 마지막 매개변수일 때, 대괄호(square brackets)는 생략할 수 있다.

키워드 리스트를 처리하기 위해, elixir는 Keyword 모듈을 제공한다.
  
키워드 리스트가 단순히 리스트이기때문에, 리스트의 선형적인 성능 특성을 갖는다는 것을 명심해야 한다. 더 긴 리스트는 키를 찾기 위해 더 긴 시간이 필요하고, 아이템의 개수를 구하기 위해 더 긴 시간이 필요한 등등의 특징을 말한다. 이러한 이유로, 키워드 리스트는 elixir에서 주로 옵션을 표현할 때 사용한다. 만약 많은 수의 아이템을 key-value 방식으로 저장하고 싶다면 맵(maps)을 사용해야 한다.

키워드 리스트에 패턴 매치를 수행할 수 있긴 하지만, 실제로는 드물게 사용된다. 이유는 리스트의 패턴 매칭은 매치를 위해 아이템의 개수와 순서를 요구하기 때문이다.

```
  
iex> [a: a] = [a: 1]
  
[a: 1]
  
iex> a
  
1
  
iex> [a: a] = [a: 1, b: 2]
  
** (MatchError) no match of right hand side value: [a: 1, b: 2]
  
iex> [b: b, a: a] = [a: 1, b: 2]
  
** (MatchError) no match of right hand side value: [a: 1, b: 2]
  
```

## Maps

key-value 저장이 필요할때마다, elixir에서는 맵을 사용하면 된다. 맵은 %{} 구문으로 생성할 수 있다.

```
  
iex> map = %{:a => 1, 2 => :b}
  
%{2 => :b, :a => 1}
  
iex> map[:a]
  
1
  
iex> map[2]
  
:b
  
iex> map[:c]
  
nil
  
```

키워드 리스트와 비교해보면 다음과 같은 두 가지 차이점을 발견할 수 있다.

  * 맵은 키에 어떤 값이라도 사용할 수 있다.
  * 맵의 키는 어떤 순서도 따르지 않는다.

만약 맵을 만들 때 중복 키를 사용하면 마지막에 사용된 키가 사용된다.

```
  
iex> %{1 => 1, 1 => 2}
  
%{1 => 2}
  
```

맵에서 모든 키가 atom이라면, 편리하게 키워드 구문을 사용할 수 있다.

```
  
iex> map = %{a: 1, b: 2}
  
%{a: 1, b: 2}
  
```

키워드 리스트에 비해서, 맵은 패턴 매칭에 매우 유용하다.

```
  
iex> %{} = %{:a => 1, 2 => :b}
  
%{:a => 1, 2 => :b}
  
iex> %{:a => a} = %{:a => 1, 2 => :b}
  
%{:a => 1, 2 => :b}
  
iex> a
  
1
  
iex> %{:c => c} = %{:a => 1, 2 => :b}
  
** (MatchError) no match of right hand side value: %{2 => :b, :a => 1}
  
```

보다시피, 맵은 주어진 맵보다 더 적은 키로 매치해도 잘 작동한다. 게다가 빈 맵은 모든 맵과 매치할 수 있다.

Map 모듈은 keyword 모듈과 매우 유사한 기능을 제공한다.

```
  
iex> Map.get(%{:a => 1, 2 => :b}, :a)
  
1
  
iex> Map.to_list(%{:a => 1, 2 => :b})
  
[{2, :b}, {:a, 1}]
  
```

맵에서 흥미로운 속성 중 하나는 atom 키에 접근해서 값을 조회하거나 수정할 수 있는 특정 구문을 제공한다는 것이다.

```
  
iex> map = %{:a => 1, 2 => :b}
  
%{:a => 1, 2 => :b}

iex> map.a
  
1
  
iex> map.c
  
** (KeyError) key :c not found in: %{2 => :b, :a => 1}

iex> %{map | :a => 2}
  
%{:a => 2, 2 => :b}
  
iex> %{map | :c => 3}
  
** (ArgumentError) argument error
  
```

접근이나 수정 구문 모두 키가 존재해야 한다. 예르 들어, :c 키를 사용할 경우에는 맵에 존재하지 않기 때문에 에러가 발생한다.

elixir 개발자는 일반적으로 맵 처리를 할 때 Map 모듈의 함수를 사용하는 대신 map.field 구문이나 패턴 매치를 사용하는 것을 선호하는데, 이유는 단정적인 프로그래밍 스타일(Assertive style of programming)을 이끌어내기 때문이다. elixir에서 단정적인 코드를 작성하는 것으로 얼마나 간결하고 빠른 소프트웨어를 작성할 수 있는지에 대한 예제와 인사이트를 보고 싶다면 [이 블로그 포스트](http://blog.plataformatec.com.br/2014/09/writing-assertive-code-with-elixir/)를 참고하면 된다.

> Map은 [EEP 43](http://www.erlang.org/eeps/eep-0043.html)과 함께 erlang VM에 최근에 소개되었다. erlang 17은 EEP의 일부분인 &#8220;small maps&#8221;를 지원한다. 이것이 의미하는 것은 맵의 최대 키 저장개수가 수십개일 때에만 좋은 성능 특성을 보여준다는 것이다. 이러한 간극을 매우기 위해, elixir는 수백 수천의 키에도 좋은 성능을 제공하는 해시 알고리즘을 사용하는 HashDict 모듈을 제공한다. 

## Dicts

elixir는 키워드 리스트와 맵 모두를 사전(Dictionary)이라 부른다. 다른 말로 말하자면, 사전은 인터페이스이고 키워드 리스트와 맵은 이 사전 인터페이스를 구현한 모듈이라고 할 수 있다.

이 사전 인터페이스는 구현에 구체적인 기능을 위임하는 API를 갖는 Dict 모듈에 정의되어 있다.(키워드 리스트와 맵이 공통으로 사용할 수 있는 API를 정의하고 있다)

```
  
iex> keyword = []
  
[]
  
iex> map = %{}
  
%{}
  
iex> Dict.put(keyword, :a, 1)
  
[a: 1]
  
iex> Dict.put(map, :a, 1)
  
%{a: 1}
  
```

Dict 모듈은 어떤 개발자라도 개발자 자신의 특수한 특성을 갖고 elixir의 현존하는 코드를 후킹할 수 있는 Dict 변종을 구현할 수 있도록 허용한다. Dict 모듈은 또한 서로 다른 사전에서 동작하는 함수를 제공한다. 예를 들어, Dict.equal?/2 함수는 서로 다른 타입의 두 사전을 비교할 수 있다.

이쯤에서 궁금할 것이다. 우리의 코드에서 Keyword, Map, Dict중 어떤 것을 사용해야 할까? 대답은 &#8220;그때 그때 달라요 &#8211; it depends.&#8221; 이다.

우리의 코드가 매개변수로 특정한 자료구조를 기대한다면, 좀더 단정적인 코드를 유도하는 각각의 모듈을 사용해라. 예를 들어, 매개변수로 키워드를 기대한다면, Dict대신 Keyword 모듈을 사용하면 된다. 하지만, 우리의 API가 어떤 사전과도 잘 작동해야 한다면, Dict 모듈을 사용하면 된다.

## 참고

  * [Elixir Keywords, maps and dicts](http://elixir-lang.org/getting-started/maps-and-dicts.html)