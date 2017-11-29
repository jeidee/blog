---
author: jeidee
categories:
- elixir
date: '2015-06-18'
guid: https://erlnote.wordpress.com/?p=503
id: 503
permalink: /2015/06/18/elixir-comprehensions-1721/
tags:
- tutorial
title: Elixir Comprehensions (#17/21)
url: /2015/06/18/elixir-comprehensions-1721
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

elixir에서는, 열거형을 반복문에서 열거하고 요소를 필터링해 다른 리스트로 반환하는 일이 매우 일반적이다. 해석식(Comprehensions)은 그러한 패턴을 쉽게 하도록 도와준다. 해석식은 for와 같은 특별한 형태를 사용해 그러한 작업을 처리할 수 있다.

예를 들면, 정수 리스트에서 각 요소의 제곱을 구해 새로운 리스트를 반환하는 작업은 다음과 같이 구현할 수 있다.

```
  
iex> for n <- [1, 2, 3, 4], do: n * n
  
[1, 4, 9, 16]
  
```

해석식은 세 부분으로 구성된다: 발생기(generator), 필터, 수집자(collectables).

## Generators and filters

위의 표현식에서, n <- [1, 2, 3, 4] 부분이 발생기이다. 발생기는 말 그대로 해석을 위한 값을 생성한다. 어떤 열거형도 발생기 표현식(generator expression)의 우측 값으로 사용할 수 있다.

```
  
iex> for n <- 1..4, do: n * n
  
[1, 4, 9, 16]
  
```

발생기 표현식은 좌측 값으로 패턴 매칭을 지원하는데 매치하지 않는 패턴은 무시(ignored) 된다. 범위 값 대신에, 우리는 :good과 :bad atom을 키로 갖는 키워드 리스트를 받아, :good 값만 제곱하기를 원한다고 상상해 보자.

```
  
iex> values = [good: 1, good: 2, bad: 3, good: 4]
  
iex> for {:good, n} <- values, do: n * n
  
[1, 4, 16]
  
```

패턴 매칭 대신에, 필터를 사용할 수 있다. 예를 들어, 우리는 3의 배수를 구해 제곱하는 식을 다음과 같이 구현할 수 있다.

```
  
iex> multiple\_of\_3? = fn(n) -> rem(n, 3) == 0 end
  
iex> for n <- 0..5, multiple\_of\_3?.(n), do: n * n
  
[0, 9]
  
```

필터가 false와 nil을 반환할 경우 해당 요소를 제거하고, 그 외의 요소만 유지한다.

해석식은 일반적으로 Enum과 Stream 모듈의 동등한 함수를 사용하는 것에 비해 훨씬 더 간결한 표현을 제공한다.
  
게다가, 해석식은 하나 이상의 발생기와 필터를 허용한다. 다음 예제는 디렉토리 리스트를 받아, 해당 디렉토리의 모든 파일을 삭제하는 예이다.

```
  
for dir <- dirs,
      
file <- File.ls!(dir),
      
path = Path.join(dir, file),
      
File.regular?(path) do
    
File.rm!(path)
  
end
  
```

변수가 해석식 안에서 할당되며, 발생기와 필터, 또는 코드 블럭 안에서 사용되고, 해석식 밖에서 사용되지 않는 다는 것을 명심하자.

## Bitstring generators

비트문자열 발생기 역시 지원되며, 비트문자열 스트림을 해석할 때 매우 유용하게 사용된다. 아래 예제는 Red,Green,Blue 색상을 갖는 바이너리 픽셀 리스트를 받아, R/G/B 세 개의 요소를 갖는 튜플 리스트로 변환한다.

```
  
iex> pixels = <<213, 45, 132, 64, 76, 32, 76, 0, 0, 234, 32, 15>>
  
iex> for <<r::8, g::8, b::8 <- pixels>>, do: {r, g, b}
  
[{213, 45, 132}, {64, 76, 32}, {76, 0, 0}, {234, 32, 15}]
  
```

비트문자열 발생기는 &#8220;표준-reqular&#8221; 열거형 발생기와 함께 쓰일 수 있고 필터 역시 제공한다.

## Results other than lists

위의 예제에서, 모든 해석식은 결과로 리스트를 반환한다. 또한 해석식의 결과가 :into 옵션을 사용해 다른 데이터 구조에 삽입될 수도 있다.

예를 들어, 비트문자열 발생기는 문자열에서 모든 공백을 쉽게 제거하기 위해 :into 옵션과 함께 사용될 수 있다.

```
  
iex> for <<c <- " hello world ">>, c != ?s, into: "", do: <<c>>
  
"helloworld"
  
```

셋(Sets), 맵(Maps) 그리고 사전(Dictionaries)는 :into 옵션에 사용할 수 있다. 일반적으로, :into 옵션은 Collectable 프로토콜을 구현한 어떤 데이터 구조도 사용할 수 있다.

:into의 일반적인 용도는 키를 사용하지 않고 맵의 값을 변형하는데 있다.

```
  
iex> for {key, val} <- %{"a" => 1, "b" => 2}, into: %{}, do: {key, val * val}
  
%{"a" => 1, "b" => 4}
  
```

스트림을 사용한 예를 보자. IO 모듈은 스트림(Enumeralbe과 Collectable 프로토콜을 구현한)을 제공하기때문에, 해석식을 사용해 입력되는 어떤 문자도 대문자로 변경해 출력하는 에코 터미널을 만들 수 있다.

```
  
iex> stream = IO.stream(:stdio, :line)
  
iex> for line <- stream, into: stream do
  
&#8230;> String.upcase(line) <> "n"
  
&#8230;> end
  
```

## 참고

  * [Elixir Comprehensions](http://elixir-lang.org/getting-started/comprehensions.html)