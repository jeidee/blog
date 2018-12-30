---
author: jeidee
categories:
- elixir
date: '2015-06-16'
guid: https://erlnote.wordpress.com/?p=475
id: 475
permalink: /2015/06/16/elixir-processes-1121/
tags:
- tutorial
title: Elixir Processes (#11/21)
url: /2015/06/16/elixir-processes-1121
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

elixir에서, 모든 코드는 프로세스 안에서 실행된다. 프로세스는 서로 분리되어 있고, 서로 동시에(concurrent) 실행되며, 메세지를 건네는 것을 통해 통신한다.
  
프로세스는 elixir의 동시성 뿐만 아니라, 분산 된 구조와 결함 내구성(fault-tolerant) 역시 제공한다.

elixir의 프로세스를 OS(운영체제)의 프로세스와 혼동하면 안된다. elixir의 프로세스는 메모리와 CPU에 있어 극단적으로 경량화되어 있다(다른 프로그래밍 언어의 쓰레드와 다르게). 이러한 이유로, 수백 수천의 프로세스가 동시에 수행되는 것이 일반적이다.

이번 챕터에서 우리는, 새로운 프로세스를 생성(spawning)하는 기본 구조뿐만 아니라 프로세스간에 메세지를 주고 받는 것도 배울 것이다.

## spawn

프로세스를 생성하는 기본 매커니즘은 자동으로 포함된(auto-imported) spawn/1 함수를 사용하는 것이다.

```
  
iex> spawn fn -> 1 + 2 end
  
#PID<0.43.0>
  
```

spawn/1 함수는 다른 프로세스에서 실행될 함수를 취한다.

spawn/1 함수가 PID(프로세스 식별자)를 반환함을 주목하자. PID가 반환된 시점에서 프로세스는, 죽어 있는 것과 같다. 생성된 프로세스는 주어진 함수를 실행하고 완료되면 곧 바로 종료된다.

```
  
iex> pid = spawn fn -> 1 + 2 end
  
#PID<0.44.0>
  
iex> Process.alive?(pid)
  
false
  
```

self/0 함수를 통해 현재 프로세스(쉘의&#8230;)의 PID를 구할 수 있다.

```
  
iex> self()
  
#PID<0.41.0>
  
iex> Process.alive?(self())
  
true
  
```

프로세스는 메세지를 주고 받을 수 있을 때 더 흥미롭다.

## send and receive

우리는 프로세스에게 send/2 함수를 통해 메세지를 보내고 receive/1 함수를 통해 메세지를 수신할 수 있다.

```
  
iex> send self(), {:hello, "world"}
  
{:hello, "world"}
  
iex> receive do
  
&#8230;> {:hello, msg} -> msg
  
&#8230;> {:world, msg} -> "won't match"
  
&#8230;> end
  
"world"
  
```

메세지가 프로세스에 보내지면, 그 메세지는 프로세스의 메일박스에 전달된다. receive/1 함수는 프로세스의 메일박스에서 주어진 패턴과 일치하는 메세지를 검색해 반환한다. receive/1 함수는 case/2 와 마찬가지로 많은 절과 가드를 지원한다.

패턴과 일치하는 어떤 메세지도 없다면, 현재 프로세스는 일치하는 메세지가 올 때까지 대기한다. 타임아웃도 명시할 수 있다.

```
  
iex> receive do
  
&#8230;> {:hello, msg} -> msg
  
&#8230;> after
  
&#8230;> 1_000 -> "nothing after 1s"
  
&#8230;> end
  
"nothing after 1s"
  
```

메일박스에 기대하는 메세지가 이미 있다면 타임아웃에 0을 지정할 수 있다.

프로세스 간에 메세지를 주고 받는 예제를 살펴보자.

```
  
iex> parent = self()
  
#PID<0.41.0>
  
iex> spawn fn -> send(parent, {:hello, self()}) end
  
#PID<0.48.0>
  
iex> receive do
  
&#8230;> {:hello, pid} -> "Got hello from #{inspect pid}"
  
&#8230;> end
  
"Got hello from #PID<0.48.0>"
  
```

쉘에서, flush/0 함수를 유용하게 사용할 수 있다. 메일박스의 모든 메세지를 꺼내 출력해 준다.

```
  
iex> send self(), :hello
  
:hello
  
iex> flush()
  
:hello
  
:ok
  
```

## Links

exlixr에서 프로세스를 생성하는 일반적인 방법은 사실 spawn\_link/1 함수를 사용하는 것이다. spawn\_link/1 예제를 보기 전에, 프로세스가 실패하면 무슨 일이 생기는지 확인해 보자.

```
  
iex> spawn fn -> raise "oops" end
  
#PID<0.58.0>

[error] Error in process <0.58.0> with exit value: &#8230;
  
```

생성된 프로세스는 여전히 동작중이지만, 에러가 출력된다. 이유는 프로세스가 격리되어 있기 때문이다. 하나의 프로세스가 실패했을 때 다른 프로세스에 전파하고자 한다면, 우리는 그들 프로세스를 연결시켜야 한다. spawn_link/1함수가 이를 가능하게 한다.

```
  
iex> spawn_link fn -> raise "oops" end
  
#PID<0.41.0>

** (EXIT from #PID<0.41.0>) an exception was raised:
      
** (RuntimeError) oops
          
:erlang.apply/2
  
```

쉘에서 실패했을 때, 쉘은 자동적으로 실패를 감지(traps)해서 근사한 형태로 출력할 뿐이다. 우리 코드에서 실제로 무슨 일이 일어났는지 이해하려면, spawn_link/1 함수를 파일에서 사용하고 실행해 봐야 한다.

```
  
\# spawn.exs
  
spawn_link fn -> raise "oops" end

receive do
    
:hello -> "let's wait until the process fails"
  
end
  
```

이번에는 프로세스가 실패하면 부모 프로세스의 중지(down)도 유발한다. Process.link/1 함수를 통해 수동적으로 연결을 만들 수도 있는데, 프로세스가 제공하는 다른 기능들을 더 살펴보려면 Process 모듈을 살펴보도록 한다.

프로세스와 링크는 결함 내구성(fault-talerant) 시스템을 만드는데 중요한 역할을 한다. elixir 응용프로그램에서는, 프로세스의 종료와 새로운 프로세스의 시작을 감지하기 위해서 슈퍼바이저와 프로세스를 링크하곤 한다. 이것이 가능한 이유는 프로세스가 기본적으로 어떤 것도 공유하지 않고 서로 격리되어 있기 때문이다. 프로세스가 격리되어 있기 때문에 다른 프로세스에 크래시와 결함 상태를 전파할 수 있는 방법이 없다.

다른 언어에서는 우리가 직접 예외를 잡아서 처리하도록 하고 있지만, elixir에서는 슈퍼바이저가 우리 시스템을 적합하게 재시작할 것을 기대하므로 프로세스가 실패하도록 내버려 두어도 된다. &#8220;빨리 실패해라 &#8211; Failing fast&#8221;가 elixir 소프트웨어를 작성할 때 우리의 일반적인 철학이다!

다음 챕터로 넘어가기 전에, elixir에서 프로세스를 만드는 일반적인 유즈 케이스 중에 하나를 더 알아보자.

## Tasks

이전 예제에서 프로세스가 크래쉬되었을 때 우리는 약간의 정보를 갖는 에러 메세지를 통지 받았다.

```
  
iex> spawn fn -> raise "oops" end
  
#PID<0.58.0>

[error] Error in process <0.58.0> with exit value: &#8230;
  
```

spawn/1 함수와 spawn\_link/1 함수에서는, 에러메세지가 VM에 직접 생성되는데다 부족한 정보만을 포함한다. 실전에서는, 개발자가 Task 모듈의 Task.start/1 함수와 Task.start\_link/1 함수를 사용해서 좀 더 명시적으로 사용할 수 있다.

```
  
iex(1)> Task.start fn -> raise "oops" end
  
{:ok, #PID<0.55.0>}

15:22:33.046 [error] Task #PID<0.55.0> started from #PID<0.53.0> terminating
  
Function: #Function<20.90072148/0 in :erl_eval.expr/5>
      
Args: []
  
** (exit) an exception was raised:
      
** (RuntimeError) oops
          
(elixir) lib/task/supervised.ex:74: Task.Supervised.do_apply/2
          
(stdlib) proc\_lib.erl:239: :proc\_lib.init\_p\_do_apply/3
  
```

좀 더 나은 에러 로깅을 하는 대신 몇 개의 차이가 있다: start/1 함수와 start_link/1 함수는 pid만 반환하지 않고 {:ok, pid} 튜플을 반환한다. 이것이 태스크가 슈퍼비전 트리에서 사용되도록 해주는 이유이다. 게다가, 태스크는 분산을 쉽게 해주는 Task.async/1과 Task.await/1과 같은 편리한 함수를 제공해 준다.

우리는 이러한 기능들을 Mix and OTP guide에서 자세히 살펴보도록 하고, 지금은 태스크가 로깅에 좀 더 좋다는 것만 기억하도록 하자.

## State

우리는 아직까지 상태(state)에 관해서는 말하지 않았다. 우리가 상태를 요하는 응용프로그램(예를 들어 응용프로그램 설정을 유지해야 하는)을 만들고 있다면, 파일을 파싱해서 메모리에 유지해야 하는데, 이 값을 어디에 저장할 것인가?

프로세스가 이 질문의 가장 일반적인 대답이다. 우리는 무한루프를 갖는 프로세스를 작성할 수 있고, 이 루프에서 상태를 유지하고, 메세지를 주고 받을 수 있다.
  
예를 들어, 키-값 쌍을 저장하는 프로세스를 생성하는 &#8220;kv.exs&#8221; 파일명을 갖는 모듈을 작성해 본다고 하자.

```
  
defmodule KV do
    
def start_link do
      
Task.start_link(fn -> loop(%{}) end)
    
end

defp loop(map) do
      
receive do
        
{:get, key, caller} ->
          
send caller, Map.get(map, key)
          
loop(map)
        
{:put, key, value} ->
          
loop(Map.put(map, key, value))
      
end
    
end
  
end
  
```

start_link 함수는 기본적으로 빈 맵과 함께 loop/1 함수를 실행하는 새로운 프로세스를 생성하는 것에 주목한다. loop/1 함수는 메세지를 기다렸다가 적합한 액션을 수행한다. :get 메세지의 경우, caller에게 메세지를 보내고 loop/1 함수를 다시 호출(tail call 최적화 &#8211; 꼬리 재귀, 스택 사용하지 않음)하여 메세지 수신을 다시 시작한다. :put 메세지의 경우 맵의 새로운 버전(메세지에 포함된 key, value를 저장한)을 loop/1 함수에 전달한다.

iex kv.exs를 수행해서 실습해보도록 하자.

```
  
iex> {:ok, pid} = KV.start_link
  
#PID<0.62.0>
  
iex> send pid, {:get, :hello, self()}
  
{:get, :hello, #PID<0.41.0>}
  
iex> flush
  
nil
  
```

처음에는, 프로세스의 맵에는 어떤 키도 없기 때문에, :get 메세지를 보내고 flushing을 해 보아도 프로세스의 inbox는 nil값만 반환하게 된다. :put 메세지를 보내고 다시 시도해 보자.

```
  
iex> send pid, {:put, :hello, :world}
  
#PID<0.62.0>
  
iex> send pid, {:get, :hello, self()}
  
{:get, :hello, #PID<0.41.0>}
  
iex> flush
  
:world
  
```

프로세스가 어떻게 상태를 유지하고 우리가 어떻게 프로세스에 메세지를 보내는 것으로 값을 조회하고 수정하는지에 주목한다. 사실, 위의 예제에서 사용된 pid를 아는 어떤 프로세스도 메세지를 보내고 상태를 처리할 수 있다.

pid에 이름을 등록해서, 이름을 아는 어떤 프로세스도 메세지를 보내게 할 수도 있다.

```
  
iex> Process.register(pid, :kv)
  
true
  
iex> send :kv, {:get, :hello, self()}
  
{:get, :hello, #PID<0.41.0>}
  
iex> flush
  
:world
  
```

elixir 응용프로그램에서 프로세스에 이름을 등록하는 것은 매우 흔한 패턴이다. 하지만 우리는 위와 같은 수동적인 방법을 사용하길 원하지 않기 때문에, Agent 모듈을 사용해서 다음과 같이 사용하도록 한다.

```
  
iex> {:ok, pid} = Agent.start_link(fn -> %{} end)
  
{:ok, #PID<0.72.0>}
  
iex> Agent.update(pid, fn map -> Map.put(map, :hello, :world) end)
  
:ok
  
iex> Agent.get(pid, fn map -> Map.get(map, :hello) end)
  
:world
  
```

:name 옵션은 Agent.start_link/2함수에 전달할 수 있고 자동적으로 등록된다(PID의 이름으로). Agent이외에도 elixir는 프로세스에 의해 구동되는 generic server(GenServer라고 불리는), generic event manager와 event handler(GenEvent라고 불리는), 태스크 등등을 만드는 API를 제공한다. 그런 것들 모두 슈퍼비전 트리에 속하며 처음부터 끝까지 elixir 응용프로그램을 완성하는데 사용되는 Mix and OTP guide에서 더 자세히 알아볼 수 있다.

## 참고

  * [Elixir Processes](http://elixir-lang.org/getting-started/processes.html#spawn)