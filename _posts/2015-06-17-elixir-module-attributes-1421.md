---
author: jeidee
categories:
- elixir
date: '2015-06-17'
guid: https://erlnote.wordpress.com/?p=494
id: 494
permalink: /2015/06/17/elixir-module-attributes-1421/
tags:
- tutorial
title: Elixir Module attributes (#14/21)
url: /2015/06/17/elixir-module-attributes-1421
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

elixir에서 모듈의 속성은 3개의 목적으로 제공된다.

  * 모듈에 주석을 제공한다. 사용자와 VM에 정보를 제공한다.
  * 상수로 동작한다.
  * 컴파일하는 동안 임시 모듈 저장공간으로 사용된다.

하나씩 살펴보자.

## As annotations

elixir는 모듈 속성 컨셉을 Erlang에서 가져왔다. 예를 보자.

```
  
defmodule MyServer do
    
@vsn 2
  
end
  
```

위의 예는, 모듈의 버전 속성을 명시적으로 설정한다. @vsn은 Erlang VM이 모듈이 수정되었는지 여부를 검사해서 코드를 리로딩할 지 결정하는 매커니즘으로 사용한다. @vsn이 명시되지 않을 경우 모듈 함수의 버전으로 MD5 체크섬이 설정된다.

elixir는 소수의 예약된 속성을 갖는다. 그들 중 흔히 사용되는 것들 몇 개만 살펴보도록 하자.

  * @moduledoc &#8211; 현재 모듈의 문서를 제공한다.
  * @doc &#8211; 함수나 매크로의 문서를 제공한다.
  * @behaviour &#8211; OTP 또는 유저가 정의한 behaviour를 명시한다.
  * @before_compile &#8211; 모듈이 컴파일될 때 불려지는 동작을 명시한다. 이것은 컴파일 타임에 특정 함수를 모듈에 주입(inject)할 수 있게 해 준다.

@moduledoc과 @doc은 흔히 사용하는 속성인데, 가능하면 많이 사용하도록 해야 한다. elixir는 문서를 일등급(first-class)으로 취급하고 문서에 접근할 수 있는 많은 함수를 제공한다.

이전 챕터에서 정의한 Math 모듈로 되돌아가 보자. math.ex 파일에 다음과 같이 문서를 추가해 보자.

```
  
defmodule Math do
    
@moduledoc """
    
Provides math-related functions.

\## Examples

iex> Math.sum(1, 2)
        
3

"""

@doc """
    
Calculates the sum of two numbers.
    
"""
    
def sum(a, b), do: a + b
  
end
  
```

elixir는 가독성 있는 문서를 작성하는데 [heredoc](http://zetawiki.com/wiki/HEREDOC)(문자열을 코드 중간에 그대로 포함)과 함께 마크다운 문법을 사용하도록 권고한다. heredoc은 여러 줄의 문자열로 이뤄져 있고 시작과 끝은 &#8220;&#8221;&#8221;(triple quotes)으로 묶으며 입력한 그대로의 형태를 유지한다. 우리는 컴파일된 모듈의 문서를 iex를 통해 볼 수 있다.

```
  
$ elixirc math.ex
  
$ iex

iex> h Math # Access the docs for the module Math
  
&#8230;
  
iex> h Math.sum # Access the docs for the sum function
  
&#8230;
  
```

우리는 [ExDoc](https://github.com/elixir-lang/ex_doc)이라고 불리는 도구를 사용해 HTML 페이지 형태의 문서를 만들 수도 있다.

지원되는 속성의 전체 리스트는 [Module](http://elixir-lang.org/docs/stable/elixir/#!Module.html) 문서에서 볼 수 있다. elixir는 또한 [typespecs](http://elixir-lang.org/docs/stable/elixir/#!Kernel.Typespec.html)를 정의해서 속성을 만들수도 있다.

  * @spec &#8211; 함수를 위한 명세를 제공
  * @callback &#8211; behaviour 콜백을 위한 명세를 제공
  * @type &#8211; @spec에서 사용되는 타입을 정의
  * @typep &#8211; @spec에서 사용되는 private 타입을 정의
  * @opaque &#8211; @spec에서 사용하는 opaque 타입(변수의 조작을 함수를 통해서만 가능하도록 하는 타입)을 정의

이 섹션에서는 내장된(built-in) 속성만 언급했다. 하지만, 속성은 개발자에 의해 사용될 뿐만 아니라 커스텀 behaviour를 지원하는 라이브러리에 의해서 확장될 수도 있다.

## As constants

elixir 개발자는 모듈 속성을 상수로 사용하기도 한다.

```
  
defmodule MyServer do
    
@initial_state %{host: "147.0.0.1", port: 3456}
    
IO.inspect @initial_state
  
end
  
```

> Erlang과 다르게, 유저가 정의한 속성은 기본적으로 모듈에 저장되지 않는다. 값은 오직 컴파일시에만 존재한다. 개발자가 Erlang과 유사하게 속성을 설정하고 싶다면 Module.register_attribute/3 함수를 사용해야 한다. 

정의되지 않은 속성에 접근하게 되면 경고를 출력할 것이다.

```
  
defmodule MyServer do
    
@unknown
  
end
  
warning: undefined module attribute @unknown, please remove access to @unknown or explicitly set it to nil before access
  
```

속성은 함수 안에서 다음과 같이 사용될 수 있다.

```
  
defmodule MyServer do
    
@my_data 14
    
def first\_data, do: @my\_data
    
@my_data 13
    
def second\_data, do: @my\_data
  
end

MyServer.first_data #=> 14
  
MyServer.second_data #=> 13
  
```

함수 안에서 속성을 읽는 순간 현재 값의 스냅샷을 가져온다는 것을 명심하자. 다른 말로, 값은 컴파일시에 읽혀질뿐, 런타임에 읽혀지는 것이 아니다. 우리가 살펴본대로, 속성은 모듈이 컴파일되는 동안 저장공간으로 유용하게 사용될 수 있다.

## As temporary storage

elixir로 구성한 프로젝트 중에 [Plug](https://github.com/elixir-lang/plug) 프로젝트가 있는데, elixir에서 웹 라이브러리와 프레임워크를 만드는데 기초가 되는 프로젝트이다.

Plug 라이브러리는 개발자가 웹서버에서 실행될 수 있는 그들 자신의 plug를 정의할 수 있게 해 준다.

```
  
defmodule MyPlug do
    
use Plug.Builder

plug :set_header
    
plug :send_ok

def set\_header(conn, \_opts) do
      
put\_resp\_header(conn, "x-header", "set")
    
end

def send\_ok(conn, \_opts) do
      
send(conn, 200, "ok")
    
end
  
end

IO.puts "Running MyPlug with Cowboy on http://localhost:4000"
  
Plug.Adapters.Cowboy.http MyPlug, []
  
```

위의 예제에서, 우리는 웹 요청이 있을 때 호출되는 함수 연결을 위해 plug/1 매크로를 사용했다. 내부적으로, 매번 plug/1을 호출할 때마다, Plug 라이브러리는 @plugs 속성에 있는 주어진 매개변수를 저장한다. 모듈이 컴파일되기 직전에, Plug는 http 요청을 처리하는 call/2 메서드를 정의하는 콜백을 실행한다. 이 메서드는 순서대로 @plugs 안에서 모든 plug를 실행할 것이다.

아래 코드를 이해하기 위해, 우리는 매크로가 필요한데, 이 매크로는 메타 프로그래밍 가이드에서 다시 한 번 살펴볼 것이다. 어쨌든, 여기에서 집중할 것은, 어떻게 모듈 속성을 저장공간으로 사용하는 것이 개발자로 하여금 DSL(?)을 생성하도록 허용하는가이다.

모듈 속성을 주석과 저장소로 사용하는 ExUnit 프레임워크의 예제를 보도록 하자.

```
  
defmodule MyTest do
    
use ExUnit.Case

@tag :external
    
test "contacts external service" do
      
\# &#8230;
    
end
  
end
  
```

ExUnit의 태그는 주석 테스트에 사용된다. 태그를 사용해 테스트를 필터링할 수 있다. 예를 들어, 느리거나 다른 서비스에 의존성이 있는 외부 테스트를 실행하지 않을 수 있다.

## 참고

  * [Elixir Module attributes](http://elixir-lang.org/getting-started/module-attributes.html)