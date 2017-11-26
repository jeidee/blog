---
id: 467
title: 'Elixir Enumerables and Streams (#10/21)'
date: 2015-06-15T17:06:50+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=467
permalink: /2015/06/15/elixir-enumerables-and-streams-1021/
categories:
  - elixir
tags:
  - tutorial
---
> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

## Enumerables

elixir는 열거가능한 자료구조(enumerables) 컨셉과 그들을 처리하기 위한 Enum 모듈을 제공한다. 우리는 이미 두 개의 열거가능한 자료구조(lists and maps)를 배웠다.

```
  
iex> Enum.map([1, 2, 3], fn x -> x * 2 end)
  
[2, 4, 6]
  
iex> Enum.map(%{1 => 2, 3 => 4}, fn {k, v} -> k * v end)
  
[2, 12]
  
```

Enum 모듈은 열거가능한 자료구조에서 transform(변형), sort(정렬), group, filter등의 처리를 통해 아이템을 얻을 수 있는 수많은 함수들을 제공한다. Enum 모듈은 elixir 코드에서 개발자들이 자주 자용하는 모듈 중 하나이다.

elixir는 범위 표현을 위해 다음 구문을 제공한다:

```
  
iex> Enum.map(1..3, fn x -> x * 2 end)
  
[2, 4, 6]
  
iex> Enum.reduce(1..3, 0, &+/2)
  
6
  
```

Enum 모듈은 서로 다른 데이터 타입을 처리하기 위해 설계되었으므로, Enum 모듈의 API는 많은 데이터 타입을 함께 처리하기 위해 함수를 제한적으로 사용한다. 특정 동작들의 경우, 모듈에 접근할 때 데이터타입을 명시해야 한다. 예를 들어, 리스트의 특정 위치에 요소를 삽입하고자 할 때, List 모듈의 List.insert_at/3 함수를 사용해야 하는데, 해당 함수는 범위에 값을 삽입하는 동작을 수행한다.

Enum 모듈의 함수는 서로 다른 데이터 타입을 처리할 수 있어야 하기 때문에, 다형성을 갖는다고 할 수 있다. 특히, Enum 모듈의 함수는 Enumerable 프로토콜을 구현하는 어떤 데이터 타입이라도 취급할 수 있다. 이 프로토콜에 대해서는 다음 챕터에서 살펴보도록 하고 먼저 스트림이라 불리는 특수한 열거형에 대해 살펴보도록 한다.

## Eager vs Lazy

Enum 모듈의 모든 함수는 즉시(eager) 평가된다. 많은 함수들은 열거한 후 리스트를 반환하기를 기대한다.

```
  
iex> odd? = &(rem(&1, 2) != 0)
  
#Function<6.80484245/1 in :erl_eval.expr/5>
  
iex> Enum.filter(1..3, odd?)
  
[1, 3]
  
```

이것은 Enum모듈과 여러 동작을 수행할 때, 각 동작은 우리가 결과를 얻을 때까지 임시 리스트를 생성하다는 것을 의미한다.

```
  
iex> 1..100_000 |> Enum.map(&(&1 * 3)) |> Enum.filter(odd?) |> Enum.sum
  
7500000000
  
```

위의 예는 동작의 파이프라인을 갖는다. 우리는 숫자의 범위에서 시작해서 각 요소에 3을 곱한다. 이 첫 번째 동작의 결과로 100\_000개의 아이템을 갖는 리스트를 반환할 것이다. 그런 후 우리는 그 리스트에서 홀수만 취하는 50\_000 개의 아이템을 갖는 리스트를 새로 만들고, 각 엔트리(요소)를 모두 더한 결과를 반환한다.

## The pipe operator

|> 기호는 파이프 연산자이다. 이 연산자는 단순히 왼쪽 연산의 결과를 오른쪽 표현식의 입력으로 건네주는 역할을 수행한다. 이 연산자는 유닉스의 | 연산자와 유사하다. 이 예제의 목적은 연속적인 함수에 의해 변형되는 데이터의 흐름을 강조하기 위한 것이다. 이 연산자가 어떻게 코드를 깨끗하게 만들어주는지, 위의 예제와 똑같이 동작하는 아래 예제와 비교해 보도록 하자.

```
  
iex> Enum.sum(Enum.filter(Enum.map(1..100_000, &(&1 * 3)), odd?))
  
7500000000
  
```

파이프 연산자에 대해 더 알고 싶다면, [이 문서](http://elixir-lang.org/docs/stable/elixir/#!Kernel.html#%7C%3E/2)를 읽어보도록 한다.

## Streams

elixir에서 Enum 모듈을 대체하는 Stream 모듈을 제공하는데, 게으른 평가(Lazy operations)을 지원한다.

```
  
iex> 1..100_000 |> Stream.map(&(&1 * 3)) |> Stream.filter(odd?) |> Enum.sum
  
7500000000
  
```

스트림은 게으르고 구성가능한 열거형이다. 즉시 리스트를 생성하지 않고, 연속된 계산만 만들어 둔 후 Enum 모듈에서 사용될 때에만 비로소 평가된다. 스트림은 큰(어쩌면 무한대의) 컬렉션에 사용하면 유용하다.

위의 예제에서 보여 주듯이, 1..100\_000 |> Stream.map(&(&1 * 3)) 표현식은 스트림의 데이터타입을 반환하고 단지 1..100\_000 사이의 map 계산만 표현하게 된다.(이 시점에 실제 계산은 수행되지 않는다.)

```
  
iex> 1..100_000 |> Stream.map(&(&1 * 3))
  
#Stream<[enum: 1..100000, funs: [#Function<34.16982430/1 in Stream.map/2>]]>
  
```

게다가 스트림은 파이프 연산자를 통해 여러 개의 스트림을 하나의 스트림으로 구성(composable)할 수 있다.

```
  
iex> 1..100_000 |> Stream.map(&(&1 * 3)) |> Stream.filter(odd?)
  
#Stream<[enum: 1..100000, funs: [&#8230;]]>
  
```

Stream 모듈의 많은 함수가 매개변수로 어떤 열거형도 받을 수 있고 결과로 스트림을 반환한다. 무한대의 스트림을 생성하는 함수도 있다. 예를 들어, Stream.cycle/1 함수는 주어진 열거형을 무한히 순환할 수 있는 스트림을 생성한다. 그런 스트림에 Enum.map/2 함수를 호출하면 영원히 순환에 빠지므로 조심해서 사용해야 한다.

```
  
iex> stream = Stream.cycle([1, 2, 3])
  
#Function<15.16982430/2 in Stream.cycle/1>
  
iex> Enum.take(stream, 10)
  
[1, 2, 3, 1, 2, 3, 1, 2, 3, 1]
  
```

Stream.unfold/2 함수는 주어진 초기 값으로부터 여러 값들을 생성하는데 사용할 수 있다.

```
  
iex> stream = Stream.unfold("hełło", &String.next_codepoint/1)
  
#Function<39.75994740/2 in Stream.unfold/2>
  
iex> Enum.take(stream, 3)
  
["h", "e", "ł"]
  
```

또다른 재밌는 함수로 리소스를 감싸는데 사용되는 Stream.resource/3의 경우 열거하기 전에 올바르게 열려 있음을 보장하고 실패가 발생할 경우 닫힘을 보장한다. 예를 들어, 우리는 파일 스트림을 위해 다음과 같이 사용할 수 있다.

```
  
iex> stream = File.stream!("path/to/file")
  
#Function<18.16982430/2 in Stream.resource/3>
  
iex> Enum.take(stream, 10)
  
```

위의 예제는 선택된 파일에서 먼저 10 줄을 가져온다. 이것은 큰 파일이나 네트워크같이 느린 리소스를 처리하는데 스트림이 매우 유용함을 뜻한다.

Enum과 Stream 모듈의 많은 양의 함수의 기능들에 압도될 수 있지만, 개별 케이스별로 매우 유사하기 때문에, 먼저 Enum 모듈에 집중하고 추후에 크고 느린 리소스를 처리할 때 Stream 모듈로 관심을 옮기는 것이 좋다.

## 참고

  * [Elixir Enumerables and Streams](http://elixir-lang.org/getting-started/enumerables-and-streams.html)