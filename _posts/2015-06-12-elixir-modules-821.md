---
id: 456
title: 'Elixir Modules (#8/21)'
date: 2015-06-12T16:09:33+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=456
permalink: /2015/06/12/elixir-modules-821/
categories:
  - elixir
tags:
  - tutorial
---
> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

elixir에서 우리는 모듈에 여러 함수를 그룹지을 수 있다. 우리는 이전 챕터에서 String 모듈 같은 여러 다른 모듈들을 사용했다.

```
  
iex> String.length "hello"
  
5
  
```

elixir에서 우리만의 모듈을 만들기 위해, 우리는 defmodule 매크로를 사용해야 한다. 우리는 우리의 모듈에서 함수를 정의하기 위해 def 매크로를 사용할 수 있다.

```
  
iex> defmodule Math do
  
&#8230;> def sum(a, b) do
  
&#8230;> a + b
  
&#8230;> end
  
&#8230;> end

iex> Math.sum(1, 2)
  
3
  
```

다음 섹션에서, 우리의 예제는 좀 더 복잡해질 것이고, 쉘에서 그 모듈이 마치 타입 처럼 동작하는 것을 보게 될 것이다. 이쯤에서 elixir 코드를 어떻게 컴파일하고 elixir 스크립트를 어떻게 실행할 수 있는지 배워보자.

## Compilation

모듈을 파일에 작성하면 컴파일하고 재사용할 수 있기 때문에 매우 편리하다. 우리가 만들 모듈이 다음과 같은 내용을 포함하며 파일명이 math.ex라고 가정하자.

```
  
defmodule Math do
    
def sum(a, b) do
      
a + b
    
end
  
end
  
```

이 파일은 elixirc를 사용해 다음과 같이 컴파일할 수 있다.

```
  
$ elixirc math.ex
  
```

컴파일 결과로 해당 모듈의 바이트코드를 포함하는 Elixir.Math.beam 파일이 생성될 것이다. iex를 다시 시작하고, 우리의 모듈이 잘 작동하는지 확인해 보자.(iex는 beam파일이 있는 디렉토리에서 실행해야 한다.)

```
  
iex> Math.sum(1, 2)
  
3
  
```

elixir 프로젝트는 일반적으로 다음 세개의 디렉토리로 구성된다.

  * ebin &#8211; 컴파일된 바이트코드를 포함
  * lib &#8211; elixir 코드(일반적으로 .ex 파일들)를 포함
  * test &#8211; 테스트 코드를 포함(일반적으로 .exs 파일들)

실제 프로젝트로 작업할 때에는, mix라고 불리는 빌드 툴을 사용해 적합한 경로를 구성하고 컴파일하게 될 것이다.

배움의 목적으로, elixir가 컴파일 과정 없이 좀 더 유연하게 사용할 수 있는 스크립트 모드를 지원한다는 것을 알아두자.

## Scripted mode

.ex 파일에 더해, elixir는 스크립팅을 위한 .exs 파일을 지원한다. elixir는 정확히 같은 방식으로 두 파일을 처리하는데, 차이는 단지 목적(용도)에 있다. .ex 파일은 컴파일 목적이고, .exs 파일은 컴파일 과정이 필요없는 스크립팅 목적으로 사용한다. 예를 들어, 우리가 다음과 같은 math.exs라는 파일을 만들었다고 보자.

```
  
defmodule Math do
    
def sum(a, b) do
      
a + b
    
end
  
end

IO.puts Math.sum(1, 2)
  
```

이 파일은 다음과 같이 실행할 수 있다.

```
  
$ elixir math.exs
  
```

해당 파일은 메모리상에서 컴파일되고 실행되어 결과로 &#8220;3&#8221;을 출력할 것이다. 어떤 바이트코드도 생성되지 않는다.

## Named functions

모듈안에서, 우리는 def/2(매개변수 2개를 갖는) public 함수를 정의하고 defp/2 private 함수를 정의할 수 있다. def/2 함수는 다른 모듈에서 호출할 수 있지만, defp/2 함수는 동일 모듈(invoked locally)에서만 호출할 수 있다.

```
  
defmodule Math do
    
def sum(a, b) do
      
do_sum(a, b)
    
end

defp do_sum(a, b) do
      
a + b
    
end
  
end

Math.sum(1, 2) #=> 3
  
Math.do_sum(1, 2) #=> ** (UndefinedFunctionError)
  
```

함수 선언 역시 복수의 절(clauses)과 가드(guards)를 지원한다. 함수가 여러 절을 갖는다면, elixir는 매치하는 절을 찾을 때까지 각 절을 평가할 것이다.

여기에 주어진 숫자가 0인지 아닌지 검사하는 함수 구현이 있다.

```
  
defmodule Math do
    
def zero?(0) do
      
true
    
end

def zero?(x) when is_number(x) do
      
false
    
end
  
end

Math.zero?(0) #=> true
  
Math.zero?(1) #=> false

Math.zero?([1,2,3])
  
#=> ** (FunctionClauseError)
  
```

주어진 매개변수와 일치하는 절을 갖는 함수가 없을 경우 에러가 발생할 것이다.

## Function capturing

지금까지 우리는 함수의 표기법을 name/arity와 같이 표기해 왔다. 이 표기는 함수 타입으로 이름 있는 함수를 받을때 사용할 수 있다. 위에서 사용한 math.exs를 iex를 사용해서 시작해 보자.

```
  
$ iex math.exs
  
```

```
  
iex> Math.zero?(0)
  
true
  
iex> fun = &Math.zero?/1
  
&Math.zero?/1
  
iex> is_function fun
  
true
  
iex> fun.(0)
  
true
  
```

지역 함수나 이미 포함된(imported) 함수(is_function/1 같은)는 모듈명 없이 캡춰할 수 있다.

```
  
iex> &is_function/1
  
&:erlang.is_function/1
  
iex> (&is_function/1).(fun)
  
true
  
```

캡춰 구문은 또한 함수를 생성하는 축약된 형태로 사용될 수도 있음을 명심한다.

```
  
iex> fun = &(&1 + 1)
  
#Function<6.71889879/1 in :erl_eval.expr/5>
  
iex> fun.(1)
  
2
  
```

&1은 함수에 전달된 첫 번째 매개변수를 표현한다. &(&1 + 1)은 정확히 fn x -> x + 1 end 와 동일하다. 위 구문은 함수 정의를 축약하는데 도움을 준다.

같은 방법으로 모듈에서 함수를 호출하고 싶다면, &Module.function() 구문을 사용할 수 있다.

```
  
iex> fun = &List.flatten(&1, &2)
  
&List.flatten/2
  
iex> fun.([1, [[2], 3]], [4, 5])
  
[1, 2, 3, 4, 5]
  
```

&List.flatten(&1, &2)은 fn(list, tail) -> List.flatten(list, tail) end와 동일하다. 캡춰 연산자에 대해 더 자세히 알고 싶다면 &#8220;[kernel.SpecialForms 문서](http://elixir-lang.org/docs/stable/elixir/#!Kernel.SpecialForms.html#&/1)&#8220;를 참고하도록 한다.

## Default arguments

elixir에서 이름있는 함수는 기본 매개변수를 지원한다.

```
  
defmodule Concat do
    
def join(a, b, sep \ " ") do
      
a <> sep <> b
    
end
  
end

IO.puts Concat.join("Hello", "world") #=> Hello world
  
IO.puts Concat.join("Hello", "world", "\_") #=> Hello\_world
  
```

어떤 표현식도 기본 값으로 사용할 수 있지만, 함수가 정의되는 동안 평가되진 않을 것이다; 단지 나중에 사용되기 위해 저장될 뿐이다. 매번 함수가 호출될때마다 기본 값은 사용되며, 기본 값이 평가된다.

```
  
defmodule DefaultTest do
    
def dowork(x \ IO.puts "hello") do
      
x
    
end
  
end
  
```

```
  
iex> DefaultTest.dowork
  
hello
  
:ok
  
iex> DefaultTest.dowork 123
  
123
  
iex> DefaultTest.dowork
  
hello
  
:ok
  
```

기본 값이 있는 함수가 복수의 절을 갖을 경우에는 함수의 선언부(구현부가 없는)가 있어야 하고 기본값을 선언하기 위한 용도로만 사용된다.

```
  
defmodule Concat do
    
def join(a, b \ nil, sep \ " ")

def join(a, b, \_sep) when is\_nil(b) do
      
a
    
end

def join(a, b, sep) do
      
a <> sep <> b
    
end
  
end

IO.puts Concat.join("Hello", "world") #=> Hello world
  
IO.puts Concat.join("Hello", "world", "\_") #=> Hello\_world
  
IO.puts Concat.join("Hello") #=> Hello
  
```

기본 값을 사용할 때, 함수 정의가 중복되지 않도록 주의해야 한다. 다음 예를 보자.

```
  
defmodule Concat do
    
def join(a, b) do
      
IO.puts "\***First join"
      
a <> b
    
end

def join(a, b, sep \ " ") do
      
IO.puts "\***Second join"
      
a <> sep <> b
    
end
  
end
  
```

위 내용을 &#8220;concat.ex&#8221;라는 파일명으로 저장하고 컴파일 하면 elixir는 다음과 같은 경고를 출력한다.

```
  
concat.ex:7: this clause cannot match because a previous clause at line 2 always matches
  
```

컴파일러는 두 개의 매개변수를 사용한 join 함수 호출시에는 항상 첫 번째 정의된 join/2함수가 호출되면 기본값이 사용된 두 번째 join/3 함수는 3개의 매개변수를 사용할 때만 호출된다고 알려준다.

```
  
$ iex concat.exs
  
```

```
  
iex> Concat.join "Hello", "world"
  
\***First join
  
"Helloworld"
  
```

```
  
iex> Concat.join "Hello", "world", "_"
  
\***Second join
  
"Hello_world"
  
```

## 참고

  * [Elixir Modules](http://elixir-lang.org/getting-started/modules.html)