---
author: jeidee
categories:
- elixir
date: '2015-06-17'
guid: https://erlnote.wordpress.com/?p=491
id: 491
permalink: /2015/06/17/elixir-alias-require-and-import-1321/
tags:
- tutorial
title: Elixir alias, require and import (#13/21)
url: /2015/06/17/elixir-alias-require-and-import-1321
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

소프트웨어 재사용을 쉽게 하기 위해, elixir는 3개의 지시자(directives)를 제공한다. 이들 지시자는 정적 유효범위([lexical scope](https://en.wikipedia.org/wiki/Scope_(computer_science)#Lexical_scope_vs._dynamic_scope))를 갖는다.(일반적인 언어에서 변수의 선언과 유효범위는 대부분 lexical scope를 갖는다.)

## alias

alias는 모듈이름의 별칭을 허용한다. 우리가 만든 Math 모듈에 특별한 List가 있다고 가정해 보자.

```
  
defmodule Math do
    
alias Math.List, as: List
  
end
  
```

이제 Math 모듈 안에서 List는 자동적으로 Math.List로 확장된다. 만약 본래의 List 모듈에 접근하고자 한다면, Elixir. 접두어(prefix)를 사용해야 한다.

```
  
List.flatten #=> uses Math.List.flatten
  
Elixir.List.flatten #=> uses List.flatten
  
Elixir.Math.List.flatten #=> uses Math.List.flatten
  
```

> elxir에 정의된 모든 모듈은 Elixir 네임스페이스에 포함되어 있다. 하지만 편리성을 위해 Elixir. 접두어는 생략할 수 있다. 

별칭은 자주 사용된다. 사실 :as 옵션을 제외하고 사용할 수 있는데, 다음 두 용법은 동일하다.

```
  
alias Math.List

alias Math.List, as: List
  
```

alias가 정적 유효범위(lexically scoped)를 갖는다는 것을 명심하자. 함수내에서 사용되었다면 해당 함수 안에서만 유효하다.

```
  
defmodule Math do
    
def plus(a, b) do
      
alias Math.List
      
\# &#8230;
    
end

def minus(a, b) do
      
\# &#8230;
    
end
  
end
  
```

위의 예제에서, alias가 plus/2 함수에서 사용되었기 때문에 plus/2 함수 안에서만 유효하게 사용된다. minus/2 함수에서는 적용되지 않는다.

## require

elixir는 메타-프로그래밍(코드를 생성하는 코드)을 위한 매커니즘을 위해 매크로가 제공된다.

매크로는 컴파일 타임에 확장되어 실행되는 코드 조각이다. 이것이 의미하는 바는, 매크로가 사용되는 모듈과 구현코드가 컴파일 될 때 해당 매크로가 유효해야 함을 뜻한다. require 지시자를 통해 이 조건을 완수할 수 있다.

```
  
iex> Integer.is_odd(3)
  
** (CompileError) iex:1: you must require Integer before invoking the macro Integer.is_odd/1
  
iex> require Integer
  
nil
  
iex> Integer.is_odd(3)
  
true
  
```

elixir에서, Integer.is\_odd/1 함수는 가드를 사용하는 매크로로 정의되어 있다. 즉, Integer.is\_odd/1 매크로를 호출하기 위해 우리는 Integer 모듈을 require해야 한다.

일반적으로 모듈은 모듈에서 매크로를 사용하기를 원하지 않는 다면, 사용하기 전에 require할 필요는 없다. 매크로를 호출할 때 로드되지 않았다면 에러가 발생할 것이다. require 지시자 역시 alias와 마찬가지로 정적 유효범위를 갖는다. 다음 챕터에서 매크로에 대해 좀 더 자세히 살펴볼 것이다.

## import

우리는 FQN(fully-qualified name)을 사용하지 않고 다른 모듈에서 함수와 매크로를 쉽게 접근하길 원할 때, import 지시자를 사용한다. 예를 들어, List모듈의 duplicate/2 함수를 사용할 때 단순히 duplicate/2만 사용하고자 한다면 다음과 같이 사용할 수 있다.

```
  
iex> import List, only: [duplicate: 2]
  
nil
  
iex> duplicate :ok, 3
  
[:ok, :ok, :ok]
  
```

이 경우에, 우리는 duplicate/2 함수만 import했다. :only는 옵션이며, 주어진 모듈에서 모든 함수가 import되는 것을 방지하고자 할 때 사용할 수 있다. 반대로 :except 옵션은 특정 함수의 리스트를 제외한 나머지를 import하고 싶을 때 사용한다.

import는 :macros와 :functions 옵션을 :only와 함께 사용할 수 있는데, 모든 매크로나 모든 함수를 지정해서 import할 때 사용한다.

```
  
import Integer, only: :macros

import Integer, only: :functions
  
```

import 또한 정적 유효범위를 갖는다.

```
  
defmodule Math do
    
def some_function do
      
import List, only: [duplicate: 2]
      
duplicate(:ok, 10)
    
end
  
end
  
```

위의 예제에서 import된 List.duplicate/2 함수는 특정 함수인 some_function/0에서만 유효하다.

import 지시자를 사용하면 자동적으로 모듈을 require한 것이 된다.

## Aliases

이쯤에서 궁금할 것이다. &#8220;Elixir&#8221; 별칭이 정확히 무엇이고 어떻게 표현되는 것일까?

elixir의 별칭은 컴파일 타임에 atom으로 변환되는 대문자로 시작하는 식별자(String, Keyword 같은)이다. 예를 들어, String 별칭은 &#8220;Elixir.String&#8221; atom으로 변환된다.

```
  
iex> is_atom(String)
  
true
  
iex> to_string(String)
  
"Elixir.String"
  
iex> :"Elixir.String" == String
  
true
  
```

alias/2 지시자를 사용해서, 우리는 별칭이 어떻게 번역되야 할지 지정할 수 있다.

Erlang 모듈의 경우 다음과 같이 atom으로 표현된다.

```
  
iex> :lists.flatten([1, [2], 3])
  
[1, 2, 3]
  
```

이것은 우리가 모듈에서 주어진 함수를 동적으로 호출할 수 있게 해 주는 매커니즘이다.

```
  
iex> mod = :lists
  
:lists
  
iex> mod.flatten([1, [2], 3])
  
[1, 2, 3]
  
```

우리는 단순히 :lists atom을 사용해서 flatten 함수를 호출할 수 있다.

## Nesting

이제 중첩에 대해 알아보자. 다음과 같은 예제가 있다.

```
  
defmodule Foo do
    
defmodule Bar do
    
end
  
end
  
```

위 예제는 두개의 모듈(Foo, Foo.Bar)을 정의하고 있다. Bar 모듈은 Foo 모듈안에서 정적 유효범위를 갖는다.

나중에 Bar 모듈이 Foo 모듈 외부로 이동할 경우, Foo.Bar와 같이 전체 이름이 필요하거나 alias를 사용해서 위와 같이 설정할 필요가 있다. Bar 모듈의 정의 역시 변경된다. 아래 예제는 위의 예제와 동일하다.

```
  
defmodule Foo.Bar do
  
end

defmodule Foo do
    
alias Foo.Bar, as: Bar
  
end
  
```

위의 코드는 정확히 다음 코드와 동일하다.

```
  
defmodule Elixir.Foo do
    
defmodule Elixir.Foo.Bar do
    
end
    
alias Elixir.Foo.Bar, as: Bar
  
end
  
```

> elixir에서는 어떤 방식으로든 모든 모듈이름이 atom으로 번역되기 때문에, Foo.Bar 모듈을 정의하기 전에 Foo 모듈을 정의할 필요는 없다. 어떤 모듈도 정의하지 않고 체인내에서 제멋대로 모듈을 중첩시킬 수 있다.(예를 들면, Foo 모듈과 Foo.Bar 모듈을 먼저 정의하지 않고 Foo.Bar.Baz 모듈을 정의하는 등의) 

## 참고

  * [Elixir alias, require and import](http://elixir-lang.org/getting-started/alias-require-and-import.html)