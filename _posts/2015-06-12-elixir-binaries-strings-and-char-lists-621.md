---
id: 447
title: 'Elixir Binaries, strings and char lists (#6/21)'
date: 2015-06-12T09:32:40+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=447
permalink: /2015/06/12/elixir-binaries-strings-and-char-lists-621/
categories:
  - elixir
tags:
  - tutorial
---
> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

&#8220;Basic types&#8221;에서 문자열에 대해 배웠고, 타입 검사를 위해 is_binary/1 함수를 사용했다.

```
  
iex> string = "hello"
  
"hello"
  
iex> is_binary string
  
true
  
```

이번 챕터에서는, 바이너리가 무엇인가를 이해하고, 문자열과 어떤 연관이 있으며, &#8221;(single-quoted value)값,&#8217;이런 값&#8217;,이 무엇을 의미하는지 살펴본다.

## UTF-8과 유니코드

문자열은 UTF-8로 인코딩된 바이너리이다. 이것이 무엇을 의미하는지 정확히 이해하기 위해서는, 바이트와 코드 포인트(bytes and code-points)의 차이가 무엇인지 먼저 이해해야 한다.

유니코드 표준은 우리가 알고 있는 많은 문자들을 코드 포인트에 할당한다. 예를 들어, a 문자는 코드 포인트 97을 갖고, ł 문자는 코드 포인트 322를 갖는다. 문자열 &#8220;hełło&#8221; 를 디스크에 쓸 때, 우리는 코드 포인트를 바이트로 변환해야 한다. 하나의 바이트가 하나의 코드 포인트를 표현한다는 룰을 적용한다면, &#8220;hełło&#8221; 를 쓸 수 없을 것이다. 왜냐하면 ł 문자의 코드 포인트는 322이며 1 바이트는 0에서 255까지의 정수값을 갖기 때문이다. 하지만 우리는 &#8220;hełło&#8221;를 스크린에서 읽을 수 있어야 하므로, 어떻게든 표현되어야 한다. 인코딩이 필요한 이유가 바로 이 때문이다.

코드 포인트를 바이트로 표현할 때, 우리는 어떻게든 인코딩해야 한다. elixir는 기본 인코딩으로 UTF-8을 사용한다. 우리가 문자열을 말할 때, UTF-8로 인코딩된 바이너리를 의미하고, 문자열은 UTF-8로 명확하게 인코딩된 특정 코드 포인트로 표현된 바이트의 묶음이라고 볼 수 있다.

우리는 ł 문자에 할당된 숫자인 322와 같은 코드 포인트를 갖기 때문에, 그것을 표현하기 위해 1바이트 이상을 필요로 한다. 이것이 바로 byte_size/1와 String.length/1가 필요한 이유이다.

```
  
iex> string = "hełło"
  
"hełło"
  
iex> byte_size string
  
7
  
iex> String.length string
  
5
  
```

UTF-8은 h, e, o의 코드 포인트를 표현하기 위해 1 바이트만을 요구하지만, ł을 표현하기 위해서는 2 바이트를 요구한다. elixir에서는 코드 포인트를 얻기 위해 ?키워드를 사용할 수 있다.

```
  
iex> ?a
  
97
  
iex> ?ł
  
322
  
```

String 모듈의 codepoints/1 함수를 사용해서 문자열을 코드 포인트별로 분리할 수 있다.

```
  
iex> String.codepoints("hełło")
  
["h", "e", "ł", "ł", "o"]
  
```

elixir는 문자열 처리에 관련된 많은 뛰어난 기능을 지원한다.. 유니코드 처리를 위한 많은 기능 역시 제공한다. 사실, elixir는 &#8220;[The string type is broken](http://mortoray.com/2013/11/27/the-string-type-is-broken/)&#8221; 아티클에서 보여주는 모든 테스트를 통과했다.

## Binaries (and bitstrings)

elixir에서 바이너리는 <<>>를 사용해서 표현한다.

```
  
iex> <<0, 1, 2, 3>>
  
<<0, 1, 2, 3>>
  
iex> byte_size <<0, 1, 2, 3>>
  
4
  
```

바이너리는 단순히 바이트의 순차배열이다. 바이트들은 어떤 식으로든 배열될 수 있는데, 꼭 유효한 문자열일 필요는 없다.

```
  
iex> String.valid?(<<239, 191, 191>>)
  
false
  
```

문자열 결합 동작은 사실 바이너리 결합 연산자를 사용하게 된다.

```
  
iex> <<0, 1>> <> <<2, 3>>
  
<<0, 1, 2, 3>>
  
```

elixir에서 일반적인 트릭으로 null 타입의 <<0>>을 결합해서 문자열 내부의 바이너리를 표현하는 방법이 있다.

```
  
iex> "hełło" <> <<0>>
  
<<104, 101, 197, 130, 197, 130, 111, 0>>
  
```

바이너리에서 각 숫자는 바이트의 표현이며, 0에서 255의 범위를 갖는다. 바이너리는 255보다 더 큰 숫자로 변경될 수 있는데, utf8로 코드 포인트가 자동 변경될 수 있다.

```
  
iex> <<255>>
  
<<255>>
  
iex> <<256>> # truncated
  
<<0>>
  
iex> <<256 :: size(16)>> # use 16 bits (2 bytes) to store the number
  
<<1, 0>>
  
iex> <<256 :: utf8>> # the number is a code point
  
"Ā"
  
iex> <<256 :: utf8, 0>>
  
<<196, 128, 0>>

```

바이트의 사이즈가 8bit일 때 1bit 크기의 값을 입력하면 어떻게 될까?

```
  
iex> <<1 :: size(1)>>
  
<<1::size(1)>>
  
iex> <<2 :: size(1)>> # truncated
  
<<0::size(1)>>
  
iex> is_binary(<< 1 :: size(1)>>)
  
false
  
iex> is_bitstring(<< 1 :: size(1)>>)
  
true
  
iex> bit_size(<< 1 :: size(1)>>)
  
1
  
```

값은 더이상 바이너리가 아니며, bitstring(단지 bit의 모음)이다! 그래서 바이너리는 비트의 개수가 8로 나누어떨어지는 bitstring이라고 할 수 있다.

바이너리와 bitstring에도 패턴 매치를 사용할 수 있다.

```
  
iex> <<0, 1, x>> = <<0, 1, 2>>
  
<<0, 1, 2>>
  
iex> x
  
2
  
iex> <<0, 1, x>> = <<0, 1, 2, 3>>
  
** (MatchError) no match of right hand side value: <<0, 1, 2, 3>>
  
```

바이너리의 각 요소(each entry)는 정확히 8bit이어야 한다. 그럼에도, 바이너리의 나머지 부분을 다음과 같이 매치할 수 있다.

```
  
iex> <<0, 1, x :: binary>> = <<0, 1, 2, 3>>
  
<<0, 1, 2, 3>>
  
iex> x
  
<<2, 3>>
  
```

문자열에서도 <> 결합 연산자를 사용해 이와 유사한 처리를 할 수 있다.

```
  
iex> "he" <> rest = "hello"
  
"hello"
  
iex> rest
  
"llo"
  
```

이것으로 문자열과 바이너리, bitstring에 대한 여정을 마친다. 문자열은 UTF-8로 인코딩된 바이너리이며, 바이너리는 8bit로 나누어 떨어지는 bitstring이다. 위의 내용들이 elixir가 비트와 바이트를 유연하게 처리한다는 것을 보여준다 하더라도, 대부분 is\_binary/1와 byte\_size/1 함수를 사용해서 바이너리를 처리하게 될 것이다.

## Char lists

문자 리스트(char list)는 문자들의 리스트 이상도 이하도 아니다.

```
  
iex> 'hełło'
  
[104, 101, 322, 322, 111]
  
iex> is_list 'hełło'
  
true
  
iex> 'hello'
  
'hello'
  
```

보시다시피, 문자 리스트는 바이트를 포함하는 대신 코드 포인트를 포함하며 &#8221;(single-quotes)에 묶어 표현할 수 있으며 리스트이다. &#8220;&#8221;(double-quotes)에 묶인 표현은 문자열(바이너리)이다.

실전에서 문자 리스트는 대부분 erlang과 인터페이싱할때 사용된다. 문자 리스트를 문자열로 변환할 때 to\_string/1을 사용하고 반대의 경우 to\_char_list/1를 사용한다.

```
  
iex> to\_char\_list "hełło"
  
[104, 101, 322, 322, 111]
  
iex> to_string 'hełło'
  
"hełło"
  
iex> to_string :hello
  
"hello"
  
iex> to_string 1
  
"1"
  
```

이들 함수는 다형성을 지니고 있다. 단순히 문자 리스트를 문자열로 변환하는 것이 아니라 정수나 atom등을 문자열로 변환할 수 있다.

## 참고

  * [Elixir Binaries, strings and char lists](http://elixir-lang.org/getting-started/binaries-strings-and-char-lists.html)