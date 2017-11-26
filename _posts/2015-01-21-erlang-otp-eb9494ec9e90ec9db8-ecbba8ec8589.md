---
id: 15
title: erlang otp 디자인 컨셉
date: 2015-01-21T23:31:47+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/erlang-otp-%eb%94%94%ec%9e%90%ec%9d%b8-%ec%bb%a8%ec%85%89/'
permalink: '/2015/01/21/erlang-otp-%eb%94%94%ec%9e%90%ec%9d%b8-%ec%bb%a8%ec%85%89/'
categories:
  - erlang
---
## supervision tree

erlang otp 디자인 컨셉의 기본은 supervision tree이다.

supervision tree는 supervisor(감독 프로세스)와 worker(실제 업무 수행 프로세스) 기반으로 모델링한 프로세스 구조이다.

  * worker는 계산 등의 실제 업무를 수행하는 프로세스이다.
  * supervisor는 worker의 행동을 모니터하는 프로세스이며 worker에 이상을 감지하면 worker를 재시작할 수 있다.
  * supervision tree는 worker와 supervisor의 계층적인 구조를 갖고, 내고장성(fault-tolerant) 소프트웨어 디자인을 가능케 한다.

> 
![Supervision Tree](http://www.erlang.org/doc/design_principles/sup6.gif) 

위 그림은 Supervision Tree이며 박스는 supervisor를, 원은 worker를 나타낸다.

## behaviours

supervision tree에서 대부분의 프로세스는 동일한 패턴에 동일한 구조를 갖는다. supervisor는 자식 프로세스를 감독하는 프로세스이고 worker는 대부분 서버-클라이언트 관계에서 서버에 해당한다.

**behaviour**는 이러한 일반 패턴을 형식화한 것이다. 이 아이디어는 코드를 일반 파트(generic part &#8211; behaviour module)와 구체 파트(specific part &#8211; callback module)로 나눈다.

behaviour 모듈은 Erlang/OTP의 일부이며 supervisor를 구현하려면 미리 정의되어 export된 callback function을 구현하면 된다.

예제를 통해 살펴보도록 하자.

다음 예제는 많은 채널을 관리하는 간단한 server를 구현한다.

```erlang
      
-module(ch1).
      
-export([start/0]).
      
-export([alloc/0, free/1]).
      
-export([init/0]).

start() ->
          
spawn(ch1, init, []).

alloc() ->
          
ch1 ! {self(), alloc},
          
receive
              
{ch1, Res} ->
                  
Res
          
end.

free(Ch) ->
          
ch1 ! {free, Ch},
          
ok.

init() ->
          
register(ch1, self()),
          
Chs = channels(),
          
loop(Chs).

loop(Chs) ->
          
receive
              
{From, alloc} ->
                  
{Ch, Chs2} = alloc(Chs),
                  
From ! {ch1, Ch},
                  
loop(Chs2);
              
{free, Ch} ->
                  
Chs2 = free(Ch, Chs),
                  
loop(Chs2)
          
end.
  
```

위의 코드에서 channels/0, alloc/1, free/2 함수는 생략되어 있음을 참고하자.
  
해당 코드는 아래에 추가해 놓았으니 실습을 할 때는 해당 코드를 내부함수로 소스에 포함하도록 한다.

간단히 코드를 살펴보면 다음과 같다.

  * start/0 함수를 사용해 서버를 시작한다.
  * start/0 함수에서는 ch1 모듈의 init함수를 매개변수 없이 호출해 프로세스를 생성한다.(spawn)
  * init/0 함수에서는 현재 프로세스(self())의 pid를 ch1 애텀에 등록하고, 현재의 채널리스트를 channels/0 함수로 구해 초기 상태로 loop/1 함수에 넘겨준다.
  * loop/1 함수에서는 현재 프로세스에서 메세지를 수신하도록 대기(동기)하는데 {From, alloc}이나 {free, Ch} 패턴의 메세지만 처리할 수 있다.
  * {From, alloc} 메세지를 수신하면 alloc/1 함수를 사용해 현재 채널리스트에서 신규 채널을 할당(Ch)하고 메세지를 전송한 프로세스에 {ch1, Ch} 메세지를 전송한 후, 새로운 상태(채널리스트 &#8211; Chs2)를 넘겨주어 loop를 지속시킨다.
  * {free, Ch} 메세지를 수신하면 해당 채널(Ch)를 free/2 함수로 해제하고, 새로운 상태(Chs2)를 loop/1 함수에 넘겨주어 loop를 지속시킨다.

ch1.erl 모듈은 서버코드(start/0, init/0)와 클라이언트코드(alloc/0, free/1)를 모두 포함하고 있다.

서버를 시작할 경우 다음과 같이 실행한다.

```erlang
      
> ch1:start().
  
```

클라이언트를 테스트할 경우 다음과 같이 실행한다.

```erlang
      
> Ch = ch1:alloc().
      
> ch1:free(Ch).
  
```

위 ch1 모듈을 otp 디자인에 맞게 다음과 같은 절차로 수정해 보도록 하자.

### 1) generic part에 해당하는 server.erl 코드를 생성한다.

```erlang
      
-module(server).
      
-export([start/1]).
      
-export([call/2, cast/2]).
      
-export([init/1]).

start(Mod) ->
          
spawn(server, init, [Mod]).

call(Name, Req) ->
          
Name ! {call, self(), Req},
          
receive
              
{Name, Res} ->
                  
Res
          
end.

cast(Name, Req) ->
          
Name ! {cast, Req},
          
ok.

init(Mod) ->
          
register(Mod, self()),
          
State = Mod:init(),
          
loop(Mod, State).

loop(Mod, State) ->
          
receive
              
{call, From, Req} ->
                  
{Res, State2} = Mod:handle_call(Req, State),
                  
From ! {Mod, Res},
                  
loop(Mod, State2);
              
{cast, Req} ->
                  
State2 = Mod:handle_cast(Req, State),
                  
loop(Mod, State2)
          
end.
  
```

generic part는 behaviour에 해당하고 callback function은 handle\_call/2와 handle\_cast/2에 해당한다.

call/2과 cast/2함수는 클라이언트 함수이며 ch1.erl 모듈에서 alloc/0과 free/1에 매치되는 함수인데 server모듈의 behaviour가 call/2, cast/2로 일반화 되었기 때문에 실제 callback 함수를 구현하는 구현 파트에서 해당 기능이 명확(specific)해 지게 된다.

### 2) specific part에 해당하며 callback 모듈인 ch2.erl을 작성한다.

```erlang
      
-module(ch2).
      
-export([start/0]).
      
-export([alloc/0, free/1]).
      
-export([init/0, handle\_call/2, handle\_cast/2]).

start() ->
          
server:start(ch2).

alloc() ->
          
server:call(ch2, alloc).

free(Ch) ->
          
server:cast(ch2, {free, Ch}).

init() ->
          
channels().

handle_call(alloc, Chs) ->
          
alloc(Chs). % => {Ch,Chs2}

handle_cast({free, Ch}, Chs) ->
          
free(Ch, Chs). % => Chs2
  
```

실제 로직은 구현 파트에 해당하는 callback 모듈에 포함된다.
  
handle\_call/2과 handle\_cast/2는 server 모듈에 의해 호출되는 callback function으로 각각 alloc/1과 free/2 함수를 실제 호출해 처리하게 된다.

위의 예제에서 알 수 있는 것은 다음과 같다.

  * server 모듈의 코드는 많은 다른 서버에서 재사용될 수 있는 일반적인 코드이다.
  * 서버의 이름을, 이 예제에서는 ch2, 클라이언트 함수를 사용하는 유저에게 숨길 수 있다. 이 것이 의미하는 것은 이름이 외부에 영향을 주지 않고 바뀔수 있음을 나타낸다.
  * 프로토콜(서버로부터 주고 받는 메세지들)을 잘 숨길 수 있다. 이 것은 실전 프로그래밍에 유용하고 인터페이스 함수를 사용하는 코드를 변경하지 않고 프로토콜을 변경할 수 있게 해 준다.
  * 우리는 ch2나 다른 콜백 모듈을 변경하지 않고, 서버를 기능적으로 확장할 수 있다.

behaviour를 사용하지 않고 더 효율적으로 코드를 작성할 수 있을지도 모르지만, 증가된 효율성은 일반화에 대한 비용으로 지불될 것이다. 시스템에서 일관된 방법을 통해 모든 어플리케이션을 관리할 수 있도록 하는 능력은 매우 중요하다.

behaviour를 사용하는 것은 다른 프로그래머들이 코드를 이해하고 읽기 쉽도록 해 준다. 즉흥적이고 임시변통인(Ad-hoc) 프로그래밍은 효율을 좀더 올려 줄 수 있겠지만 항상 이해하기 어렵게 만든다.

위의 server 모듈은, Erlang/OTP behaviour인 gen_server 모듈과 일치한다.

표준 Erlang/OTP behaviour는 다음과 같다.

  * gen_server : client-server에서 server를 구현하는데 사용
  * gen_fsm : 유한 상태 기계를 구현할 때 사용
  * gsn_event : 이벤트 핸들링을 구현할 때 사용
  * supervisor : supervision tree에서 supervisor를 구현할 때 사용

컴파일러는 behaviour(Behaviour) 구문을 이해하고 callback 함수를 구현하지 않을 경우 다음과 같이 경고를 발생시킨다.

```erlang
      
-module(chs3).
      
-behaviour(gen_server).
      
&#8230;

3> c(chs3).
      
./chs3.erl:10: Warning: undefined call-back function handle_call/3
      
{ok,chs3}
  
```

## applications

Erlang/OTP는 각각이 특정한 기능을 수행하는 많은 컴포넌트로 구성되어 있다. 컴포넌트는 Elang/OTP에서 applications란 용어로 불린다. Erlang/OTP applications의 예로는 Mnesia, Debugger를 들 수 있다. Erlang/OTP 기반의 최소한의 시스템은 Kernel과 STDLIB application으로 구성한다.

application 컨셉은 program 구조인 process와 directory 구조인 module 모두에 적용된다.

가장 간단한 구조의 application은 process 없이 module로만 구성할 수 있다. 그런 application을 library application이라고 부른다. library application의 예는 STDLIB를 들 수 있다.

process를 포함하는 application은 표준 behaviour를 사용해서 supervision tree로 가장 간단하게 구현할 수 있다.

application을 프로그래밍하는 방법은 [Applications](http://www.erlang.org/doc/design_principles/applications.html)를 참고하도록 한다.

## releases

release는 Erlang/OTP application의 서브셋과 user가 구현한 application의 셋으로 구성된 완전한 시스템을 말한다.

release와 관련된 자세한 내용은 [Releases](http://www.erlang.org/doc/design_principles/release_structure.html)를 참고하도록 한다.

타겟 환경에 release를 설치하는 방법은 System Principles의 Target Systems에 기술되어 있다.

## release handling

release handling은 release의 업그레이드와 다운그레이드와 관련된 내용을 포함한다.
  
이와 관련된 자세한 내용은 [Release Handling](http://www.erlang.org/doc/design_principles/release_handling.html)을 참고하도록 한다.

## reference

  * [Erlang OTP Design Overview](http://www.erlang.org/doc/design_principles/des_princ.html)