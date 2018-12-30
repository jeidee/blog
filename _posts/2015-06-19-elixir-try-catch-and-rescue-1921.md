---
author: jeidee
categories:
- elixir
date: '2015-06-19'
guid: https://erlnote.wordpress.com/?p=528
id: 528
permalink: /2015/06/19/elixir-try-catch-and-rescue-1921/
tags:
- tutorial
title: Elixir try, catch and rescue (#19/21)
url: /2015/06/19/elixir-try-catch-and-rescue-1921
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

elixir는 세 개의 에러 매커니즘을 갖는다: errors, throws, exits. 이번 챕터에서 우리는 이들 각각을 살펴보도록 한다.

## Errors

에러(또는 예외)는 코드에서 예외적인 상황이 발생할 때 사용된다. 숫자를 atom에 더할 때 발생할 수 있는 에러를 살펴보자.

```
  
iex> :foo + 1
  
** (ArithmeticError) bad argument in arithmetic expression
       
:erlang.+(:foo, 1)
  
```

raise/1 함수를 사용하면 아무 때나 런타임에 에러를 발생시킬 수 있다.

```
  
iex> raise "oops"
  
** (RuntimeError) oops
  
```

raise/2함수를 사용하면 에러명과 키워드 리스트를 매개변수로 넘겨 다른 에러를 발생시킬 수 있다.

```
  
iex> raise ArgumentError, message: "invalid argument foo"
  
** (ArgumentError) invalid argument foo
  
```

우리는 우리의 모듈에서 defexception을 사용해 우리만의 에러를 정의할 수 있다. 이 방법을 사용하면 에러이름이 모듈명과 동일해진다. 일반적인 방법은 메세지 필드와 함께 커스텀 예외를 정의하는 것이다.

```
  
iex> defmodule MyError do
  
iex> defexception message: "default message"
  
iex> end
  
iex> raise MyError
  
** (MyError) default message
  
iex> raise MyError, message: "custom message"
  
** (MyError) custom message
  
```

try/rescue 구문을 사용하면 에러를 처리할 수 있다.

```
  
iex> try do
  
&#8230;> raise "oops"
  
&#8230;> rescue
  
&#8230;> e in RuntimeError -> e
  
&#8230;> end
  
%RuntimeError{message: "oops"}
  
```

위의 예제는 런타임 에러를 처리하며 iex 세션에 에러 자체를 출력한다. 실제로는, try/rescue 구문은 드물게 사용된다. 예를 들어, 많은 언어에서 파일이 성공적으로 열리지 않았을 경우 에러를 발생하고 처리하도록 한다. elixir는 그 대신에 파일을 연후 그 결과를 튜플로 반환하는 File.read/1 함수를 제공한다.

```
  
iex> File.read "hello"
  
{:error, :enoent}
  
iex> File.write "hello", "world"
  
:ok
  
iex> File.read "hello"
  
{:ok, "world"}
  
```

여기에서 try/rescue를 사용하진 않았다. 우리가 파일을 열었을 때 발생하는 여러 상황을 처리하길 원한다면, 단순히 case 구문을 사용하면 된다.

```
  
iex> case File.read "hello" do
  
&#8230;> {:ok, body} -> IO.puts "Success: #{body}"
  
&#8230;> {:error, reason} -> IO.puts "Error: #{reason}"
  
&#8230;> end
  
```

언젠가, 파일을 열때 예외를 발생할지 말지 결정해야할 때가 올 것이다. 이것이 바로 elixir가 File.read/1과 그 외의 많은 함수에서 예외를 발생시키지 않는 이유이다. 대신에, 개발자가 스스로 최선의 처리를 하도록 해 준다.

파일이 존재하길 기대하는 경우라면(없을 경우 에러가 발생해야만 하는 경우) 우리는 단순히 File.read!/1 함수를 사용하면 된다.

```
  
iex> File.read! "unknown"
  
** (File.Error) could not read file unknown: no such file or directory
      
(elixir) lib/file.ex:305: File.read!/1
  
```

표준 라이브러리의 많은 함수가 이러한 패턴(에러를 발생시키는 함수와 튜플을 반환하는 함수를 쌍으로 갖는)을 따른다. 이러한 관습에 따라 foo 함수와 foo! 함수는 각각 예외 없이 튜플을 반환하거나 실패할 경우 에러를 발생시키는 방식으로 구현되어 있다. File 모듈이 이러한 관습의 대표적인 예이다.

elixir에서 우리는 흐름 제어를 위해 try/rescue 구문을 사용하지 않는 것이 좋다. 에러는 글자 그대로 에러로 취급해야 한다. 즉, 에러는 기대하지 않은 상황이며 예외적인 상황에 사용됨을 확실히 하는 것이다. 흐름 제어가 필요하다면, throws를 사용해라.

## Throws

elixir에서 값을 던져서 나중에 받을 수 있다. throw와 catch는 이와 같은 상황에서 사용되는 구문이다.

이런 상황은 적합한 API를 제공하지 않는 라이브러리와 인터페이스하는 경우를 제외하고는 실제로 흔하지 않다. 예를 들어, Enum 모듈이 값을 찾는 API를 제공하지 않는 상황에서 숫자 리스트에서 13의 첫 번째 배수를 찾는다고 가정해 보자.

```
  
iex> try do
  
&#8230;> Enum.each -50..50, fn(x) ->
  
&#8230;> if rem(x, 13) == 0, do: throw(x)
  
&#8230;> end
  
&#8230;> "Got nothing"
  
&#8230;> catch
  
&#8230;> x -> "Got #{x}"
  
&#8230;> end
  
"Got -39"
  
```

Enum은 적합한 API를 제공하기 때문에, 사실 Enum.find/2는 잘 동작할 것이다.

```
  
iex> Enum.find -50..50, &(rem(&1, 13) == 0)
  
-39
  
```

## Exits

모든 elixir 코드는 서로 통신하는 프로세스 안에서 실행된다. 프로세스가 &#8220;자연스럽게 야기된 오류 &#8211; natural causes&#8221;로 죽는다면(예를 들면 처리되지 않은 예외같은), 프로세스는 exit 시그널을 보낸다. 프로세스는 exit 시그널을 명시적으로 보내는 것으로 죽을 수도 있다.

```
  
iex> spawn_link fn -> exit(1) end
  
#PID<0.56.0>
  
** (EXIT from #PID<0.56.0>) 1
  
```

위의 예제에서, 연결된 프로세스는 1의 값을 갖는 exit 시그널을 보내는 것에 의해 죽게된다. elixir는 자동적으로 해당 메세지를 처리하고 터미널에 결과를 출력한다.

exit는 try/catch 구문에서 처리될 수 있다.

```
  
iex> try do
  
&#8230;> exit "I am exiting"
  
&#8230;> catch
  
&#8230;> :exit, _ -> "not really"
  
&#8230;> end
  
"not really"
  
```

try/catch를 사용하는 것도 드물지만 exit를 처리하는 것도 매우 드문 경우이다.

exit 시그널은 Erlang VM에 의해 제공되는 결함 내구성(fault-tolerant) 시스템의 부분으로서 아주 중요하다. 프로세스는 일반적으로 감독되는 프로세스로 부터 exit 시그널을 기다리는 슈퍼비전 트리 안에서 실행된다. exit 시그널을 한 번 받으면, 슈퍼바이저 전략에 의해 kick된 후 곧 바로 재시작 된다.

이것이 바로 elixir에서 try/catch, try/rescue가 일반적이지 않은 이유이다. 에러를 처리(복구)하는 대신, &#8220;빨리 실패-fail fast&#8221;하고, 슈퍼비전 트리에서 에러가 없는 초기 상태로 재빨리 복구되도록 보장하는 것이 더 중요하다.

## After

때때로, 에러가 발생할 수 있는 잠재적인 어떤 액션 이후에 리소스를 깨끗이 비워지도록 하는 것이 필요하다. try/after 구문은 우리가 이러한 처리를 할 수 있도록 해 준다. 예를 들어, 우리가 파일을 열고 항상 닫히길 원한다면 try/after 구문을 사용하면 된다.

```
  
iex> {:ok, file} = File.open "sample", [:utf8, :write]
  
iex> try do
  
&#8230;> IO.write file, "olá"
  
&#8230;> raise "oops, something went wrong"
  
&#8230;> after
  
&#8230;> File.close(file)
  
&#8230;> end
  
** (RuntimeError) oops, something went wrong
  
```

## Varialbes scope

try/catch/rescue/after 블럭 안에서 사용된 변수가 문맥 밖으로 누출되지 않게 하는 것은 매우 중요하다. 변수가 처음 바인드 되기 전에 try 블록에서 실패하게 되는 상황때문이다. 달리 말해, 코드가 유효하지 않게 되는 것이다.

```
  
iex> try do
  
&#8230;> from_try = true
  
&#8230;> after
  
&#8230;> from_after = true
  
&#8230;> end
  
iex> from_try
  
** (RuntimeError) undefined function: from_try/0
  
iex> from_after
  
** (RuntimeError) undefined function: from_after/0
  
```

## 참고

  * [Elixir try, catch and rescue](http://elixir-lang.org/getting-started/try-catch-and-rescue.html)