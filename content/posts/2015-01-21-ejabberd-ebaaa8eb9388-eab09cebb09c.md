---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-%eb%aa%a8%eb%93%88-%ea%b0%9c%eb%b0%9c/
id: 25
permalink: /2015/01/21/ejabberd-%eb%aa%a8%eb%93%88-%ea%b0%9c%eb%b0%9c/
tags:
- custom module
title: ejabberd 모듈 개발
url: /2015/01/21/ejabberd-ebaaa8eb9388-eab09cebb09c
---

## ejabberd 모듈 개발 공식 문서 번역

  * [원문](https://www.process-one.net/en/wiki/ejabberd_module_development/)

### 소개

ejabberd의 내부 모듈은 플러그인처럼 작동합니다. 각 모듈들은 &#8220;mod_&#8221;로 시작하는 erlang 모듈입니다. 만약 erlang을 모른다면 [erlang 입문서](http://erlang.org/doc/getting_started/)를 먼저 보고 오세요.

### 모듈 API

모든 모듈은 &#8220;gen_mod&#8221; behavior를 사용해야 합니다.

해당 behavior는 다음 API를 제공합니다:

```erlang
      
start(Host, Opts) -> ok
      
stop(Host) -> ok
      
* Host = string()
      
* Opts = [{Name, Value}]
      
* Name = Value = string()
  
```

Host는 모듈이 동작하는 가상호스트의 이름입니다. start/2와 stop/1 함수는 서버의 시작과 중지시에 각 가상호스트에 의해 호출됩니다.
  
Opts는 모듈을 위한 환경설정(configuration) 파일에 있는 옵션집합의 목록입니다. Opts는 [gen\_mod:get\_opt/3](https://www.process-one.net/en/wiki/gen_mod/) 함수로 받을 수 있습니다.

### ejabberd API

모듈들은 다음 메커니즘을 하나 이상 사용해서 ejabberd와 상호작용합니다.

  * [ejabberd core modules](https://www.process-one.net/en/wiki/ejabberd_core_modules/)
  * [ejabberd events and hooks](https://www.process-one.net/en/wiki/ejabberd_events_and_hooks/)
  * [ejabberd IQ handlers](https://www.process-one.net/en/wiki/ejabberd_IQ_handlers/)
  * [ejabberd route table](https://www.process-one.net/en/wiki/ejabberd_route_table/)
  * [ejabberd HTTP request handlers](https://www.process-one.net/en/wiki/ejabberd_HTTP_request_handlers/)

#### core modules

##### Router (ejabberd_router)

이 모듈은 각 노드에서 XMPP 패킷 처리를 위한 메인 라운터입니다. 패킷을 목적지 도메인에 기반해 라우트합니다. 이 모듈은 local 과 global routes 테이블을 갖습니다. 먼저 각 패킷의 목적지 도메인을 local 테이블에서 찾아 해당 패킷을 적합한 process에 라우트합니다. 못 찾을 경우 global 테이블에서 찾아 적합한 ejabberd node나 process에 라우트합니다. 두 테이블에서 모두 찾을 수 없을 경우, 해당 패킷은 S2S manager에 보냅니다.

  * [Router API](https://www.process-one.net/en/wiki/ejabberd_router/)

##### Local router (ejabberd_local)

이 모듈은 서버의 가상 호스트중 하나와 일치하는 목적지 도메인을 갖는 패킷을 라우트합니다. 목적지 JID의 유저 파트가 비어있지 않다면, 패킷을 session manager에 보내고 그렇지 않을 경우 패킷의 내용에 기반해 처리합니다.

  * [Local router API](https://www.process-one.net/en/wiki/ejabberd_local/)

##### Session manager (ejabberd_sm)

현재 열린 세션들을 처리합니다.

이 모듈은 패킷을 로컬 유저에게 라우트합니다. 이 모듈은 presence 테이블을 통해 어떤 유저 리소스(디바이스)에 패킷을 보내야 할지 결정합니다. 유저 리소스가 이 노드에 연결되어 있다면 C2S 매니저에 라우트합니다. 다른 노드에 연결되어 있다면 패킷은 해당 노드의 세션 매니저에 보내집니다.

  * [Session manager API](https://www.process-one.net/en/wiki/ejabberd_sm/)

##### Utilities (jlib)

각종 헬퍼 함수들입니다. 모듈들은 [jlib.hrl](https://forge.process-one.net/browse/ejabberd/trunk/src/jlib.hrl?r=trunk)에 정의된 레코드들을 사용하기 위해서 jlib.hrl을 포함(include)해야 합니다.

  * [jlib API](https://www.process-one.net/en/wiki/jlib/)

##### Modules utilities (gen_mod)

유용한 유틸리티 함수들이 있습니다. 이 모듈은 &#8220;gen_mod&#8221; behavior를 정의합니다.

  * [gen mod API](https://www.process-one.net/en/wiki/gen_mod/)

##### XML utilities (xml)

XML 패킷을 처리하는 각종 유틸리티들을 포함합니다.

  * [XML API](https://www.process-one.net/en/wiki/xml/)

##### 관계형 데이터베이스 (ejabberd_odbc)

SQL 쿼리를 실행하는데 사용합니다.

  * [Relational database API](https://www.process-one.net/en/wiki/ejabberd_odbc/)

#### events and hooks

event: 정의된 이벤트
  
hook: 이벤트가 발생될 때 호출될 콜백함수

##### 소개

ejabberd는 이벤트 매커니즘을 제공합니다. 각 모듈은 이벤트가 발생할 때 호출되는 후크를 등록할 수 있습니다.

##### 예제

mod_offline.elr은 어떻게 events/hooks 매커니즘이 사용될 수 있는지를 보여주는 예제 모듈 입니다.

##### API

```erlang
      
ejabberd_hooks:add(Hook, Host, Module, Function, Priority)
      
ejabberd_hooks:delete(Hook, Host, Module, Function, Priority)
      
* Hook = atom()
      
* Host = string() | global
      
* Module = atom()
      
* Function = atom()
      
* Priority = integer()
  
```

  * Hook: 이벤트의 이름
  * Host: 이벤트와 관련된 가상 호스트의 이름이거나 &#8216;global&#8217; atom. 일부 이벤트는 &#8216;global&#8217; atom일때만 동작하도록 설계되었습니다.
  * Module and Function: 이벤트가 발생할 때 호출되는 콜백함수(the hook)
  * Priority: 여러 개의 hook이 동일 이벤트에 등록되어 있을 때 호출되는 우선 순위. 후크간 의존성이 있을 때 사용합니다.

후크에서 ejabberd가 다른 함수를 호출하는 것(call chain)을 막고 싶다면, stop atom()을 반환해야 합니다. 호출 체인은 막는 다른 방법은 {stop, Val} 튜플을 반환하는 것입니다.

##### 이벤트 목록

사용가능한 이벤트의 목록과 후크에 전달되는 매개변수의 각종 타입에 대한 설명은 다음과 같습니다.

**events**
  
아래 목록이외에도 최근 버전에 추가된 문서화되지 않은 이벤트가 많이 있습니다.

adhoc\_local\_items(Acc, From, To, Lang) -> Adhoc
  
adhoc\_sm\_items(Acc, From, To, Lang) -> Adhoc
  
c2s\_stream\_features(Acc)
  
c2s\_unauthenticated\_iq(Acc, Server, IQ) -> Adhoc
  
disco\_local\_features(Acc, From, To, Node, Lang) -> Adhoc
  
disco\_local\_identity(Acc, From, To, Node, Lang) -> Adhoc
  
&#8211; 이 이벤트는 [XEP-0030](http://xmpp.org/extensions/xep-0030.html)과 관련되어 있습니다.
  
disco\_local\_items(Acc, From, To, Node, Lang) -> Adhoc
  
&#8211; 이 이벤트는 [XEP-0030](http://xmpp.org/extensions/xep-0030.html)과 관련되어 있습니다.
  
disco\_sm\_features(Acc, From, To, Node, Lang) -> Adhoc
  
disco\_sm\_identity(Acc, From, To, Node, Lang) -> Adhoc
  
disco\_sm\_items(Acc, From, To, Node, Lang) -> Adhoc
  
ejabberd\_ctl\_process(Args) -> CtlStatus
  
filter_packet({From, To, Packet}) -> {From, To, Packet}
  
&#8211; 이 이벤트에 후크를 등록하면 Host=global 을 전달 받습니다.
  
local\_send\_to\_resource\_hook(From, To, Packet) -> ok
  
offline\_message\_hook(From, To, Packet) -> ok
  
&#8211; 메세지가 오프라인 유저에게 전달될 때 트리거됩니다. ejabberd 세션 매니저는 offline\_message\_hook 체인(서비스 불능 에러와 함께 들어오는 메세지를 넘기는)에 기본 함수를 추가합니다. 이 후크는 체인에 아무것도 남아있지 않을때 실행되도록 가장 낮은 우선순위를 갖습니다.
  
privacy\_check\_packet(Acc, User, Server, PrivacyList, {From, To, Packet}, Dir) -> Auth
  
privacy\_get\_user_list(Acc, User, Server) -> PrivacyList
  
privacy\_iq\_get(Acc, From, To, IQ) -> {result, Packet} | {error, Error}
  
privacy\_iq\_set(Acc, From, To, IQ) -> {result, Packet} | {error, Error}
  
privacy\_updated\_list(Acc, OldPrivacyList, NewPrivacyList) -> PrivacyList
  
pubsub\_publish\_item(From, To, Node, ItemID, Payload) -> ok
  
remove_user(User, Server) -> ok
  
resend\_offline\_messages_hook(Acc, User, Server) -> [Route]
  
resend\_subscription\_requests_hook(Acc, User, Server) -> [Packet]
  
roster_get(Acc, {User, Server}) -> [RosterItem]
  
roster\_get\_jid_info(Acc, User, Server, JID) -> {Subscription, Groups}
  
roster\_get\_subscription_lists(Acc, User, Server) -> {[FromSubscription], [ToSubscription]}
  
roster\_in\_subscription(Acc, User, Server, JID, SubscriptionType, Reason) -> bool()
  
roster\_out\_subscription(User, Server, JID, SubscriptionType) -> bool()
  
roster\_process\_item(RosterItem, Server) -> RosterItem
  
set\_presence\_hook(User, Server, Resource, Packet) -> none
  
&#8211; 이 후크는 연결된 유저가 서버에 presence stanza를 보낼때는 언제나 처리됩니다.
  
sm\_register\_connection_hook(SID, JID) -> ok
  
sm\_remove\_connection_hook(SID, JID, SessionInfo) -> ok
  
unset\_presence\_hook(User, Server, Resource, Status) -> void()
  
user\_available\_hook(JID) -> ok
  
user\_receive\_packet(JID, From, To, Packet) -> ok
  
&#8211; 이 후크는 서버가 클라이언트에 통과시킬 stanza를 생성할때는 언제나 처리됩니다. 예를 들어: A유저가 MUC X(Multi User Chat)에 메세지를 보내고, X에는 A와 B 유저가 있다고 가정할 때, 이 후크는 2번 처리됩니다: 한 번은 (JID=A, From=X, To=A, Packet=Payload), 다른 한 번은 (JID=B, From=X, To=B, Packet=Payload).
  
user\_send\_packet(From, To, Packet) -> ok
  
&#8211; 이 후크는 클라이언트가 서버에 stanza를 보내고 서버가 그것을 목적지로 전달할 때는 언제나 처리됩니다. 예를 들어: A 유저가 B 유저에게 메세지를 보낼 때(From=A, To=B, Packet=Payload); A 유저가 MUC X에 메세지를 보낼 때(From=A, To=X, Packet=Payload).

**types**

To = From = JID = ServerJID = #jid (see jlib)
  
Packet = Payload = {xmlelement, Name, Attrs, SubEl}
  
IQ = #iq (see jlib)
  
Error = ?STANZA_ERROR/3 (see jlib.h)
  
Lang = string()
  
Dir = in | out
  
Auth = allow | deny
  
PrivacyList = OldPrivacyList = NewPrivacyList = none | #userlist
  
CtlStatus = false | ?STATUS\_SUCCESS | ?STATUS\_ERROR | ?STATUS\_USAGE | ?STATUS\_BADRPC (see ejabberd_ctl.hrl&#8221;)
  
Adhoc = {result, I} | {error, Error} | empty
  
Arg = [string()]
  
Node = [string()]
  
ItemID = string()
  
Route = {route, From, To, Packet
  
RosterItem = #roster (see mod_roster.hrl)
  
Subscription = none | from | to | both | remove
  
SubscriptionType = subscribe | subscribed | unsubscribe | unsubscribed
  
Reason = string()
  
Groups = [string()]
  
SimpleJID = FromSubscription = ToSubscription = {User, Server, Resource}
  
User = string()
  
Server = string()
  
Resource = string()
  
Status = string()
  
SID = {Time, pid()}
  
Time = {MegaSecs, Secs, MicroSecs} (see erlang:now/0)
  
MegaSecs = Secs = MicroSecs = int()
  
Acc = same type as the return type of the function. Each module adds to the accumulator Acc their contribution

#### IQ handlers

##### 소개

ejabberd의 내부 모듈들은 특정 네임스페이스를 사용해서 IQ를 처리하기 위해 스스로 등록할 수 있습니다.(events and hooks 매커니즘과 유사한 방법으로)

##### 예제

mod_last.erl은 IQ 처리 매커니즘을 사용한 예입니다. 해당 모듈은 events and hooks 매커니즘 역시 사용합니다.

##### API

```erlang
  
gen\_iq\_handler:add\_iq\_handler(Scope, Host, Namespace, Module, Function, IQDisc)
  
gen\_iq\_handler:remove\_iq\_handler(Scope, Host, Namespace, Module, Function, IQDisc)

* Scope = ejabberd\_local | ejabberd\_sm
      
* Namespace = string() (some namespaces macros are defined in jlib.hrl)
      
* Host = string()
      
* Module = atom()
      
* Fonction = atom()
      
* IQDisc = no\_queue | one\_queue | {queues, N} | parallel
      
* N = integer()
  
```

ejabberd\_local Scope는 서버에 지정된 IQ를 등록합니다. ejabberd\_sm Scope는 계정의 순수 JID(bare JID, resource 파트가 없는)-서버파트가 서버인-에 지정된 IQ를 등록합니다.
  
Host는 IQ와 관계된 가상 호스트 이름입니다.
  
Module과 Function은 IQ가 수신될 때 호출되는 처리 함수입니다. 처리 함수는 다음 매개변수 타입을 가져야 합니다:

```erlang
  
Module:Function(From, To, IQ) -> IQ
      
* From = To = #jid
      
* IQ = #iq
  
```

처리함수는 결과 IQ를 반환해야 합니다.
  
IQDisc는 동시에 수신된 IQ(concurrent IQ)를 어떻게 처리할지 설명합니다:
  
* no_queue: 핸들러를 실행하기 위해 어떤 쓰레드도 만들지 않는다.
  
* one_queue: 핸들러를 실행하기 위해 독점 쓰레드 하나만 생성한다.
  
* {queues, N}: 핸들러를 실행하기 위해 N개의 쓰레드를 생성한다.
  
* parallel: 각각의 IQ를 처리하기 위한 개별 쓰레드를 생성한다.

#### route table

##### 소개

ejabberd 내부 모듈은 XMPP 이름을 갖는 서버의 라우트 테이블에 스스로를 등록할 수 있습니다. 이 모듈들은 &#8220;services&#8221; 로 알려져 있습니다.
  
서비스 모듈들은 &#8220;gen\_mod&#8221;에 더해 &#8220;gen\_server&#8221; behavior를 사용해야 합니다.

##### 예제

mod_echo.erl 모듈은 라우트 매커니즘을 사용한 좋은 예제입니다.

##### API

```erlang
  
ejabberd\_router:register\_route(Host)
  
ejabberd\_router:unregister\_route(Host),
      
* Host = string()
  
```

Host는 모듈의 XMPP 이름입니다.

##### gen_server API

다음 세 함수들은 아래 처럼 모듈의 API로 사용될 수 있습니다.
  
PROCNAME 매크로는 커스텀 모듈에서 정의한 매크로입니다.

```erlang
  
start_link(Host, Opts) ->
      
Proc = gen\_mod:get\_module_proc(Host, ?PROCNAME),
      
gen\_server:start\_link({local, Proc}, ?MODULE, [Host, Opts], []).

start(Host, Opts) ->
      
Proc = gen\_mod:get\_module_proc(Host, ?PROCNAME),
      
ChildSpec =
      
{Proc,
       
{?MODULE, start_link, [Host, Opts]},
       
temporary,
       
1000,
       
worker,
       
[?MODULE]},
      
supervisor:start\_child(ejabberd\_sup, ChildSpec).

stop(Host) ->
      
Proc = gen\_mod:get\_module_proc(Host, ?PROCNAME),
      
gen_server:call(Proc, stop),
      
supervisor:terminate\_child(ejabberd\_sup, Proc),
      
supervisor:delete\_child(ejabberd\_sup, Proc).
  
```

##### gen_server callbacks

다음 함수들은 커스텀 모듈에서 정의되고 export되어야 합니다.

```erlang
  
init([Host, Opts]) -> {ok, State} |
                               
{ok, State, Timeout} |
                                
ignore |
                               
{stop, Reason}

handle_info(Info, State) -> {noreply, State} |
                                         
{noreply, State, Timeout} |
                                         
{stop, Reason, State}
  
* Info = {route, From, To, Packet}
  
* To = From = #jid (see jlib core module)
  
* Packet = {xmlelement, Name, Attrs, SubEl}

terminate(Reason, State) -> void()

handle\_call(stop, \_From, State) ->
      
{stop, normal, ok, State}.

handle\_cast(\_Msg, State) ->
      
{noreply, State}.

code\_change(\_OldVsn, State, _Extra) ->
      
{ok, State}.
  
```

init 콜백은 모듈을 초기화하기 위해 사용됩니다. Host는 모듈이 실행되는 가상 호스트의 이름입니다. Opts는 설정 파일(ejabberd.yml)에 정의된 모듈 옵션 셋의 목록이며 gen\_mod:get\_opt/3 함수로 가져올 수 있습니다. [ejabberd\_router:register\_route/1](https://www.process-one.net/en/wiki/ejabberd_router/) 함수는 이 콜백에서 실행합니다.

terminate/2 함수는 모듈을 중지할 때 사용합니다. ejabberd\_router:unregister\_route 함수는 이 콜백에서 실행합니다. handle_info/2 함수는 모듈에 보낸 XMPP 패킷을 수신하기 위해 사용합니다. [ejabberd_router:route/3]() 함수는 패킷을 리라우트(reroute)하기 위해 사용합니다.
  
모든 다른 콜백은 위에 보여진대로 쓰여질 수 있습니다.

#### HTTP request handlers

ejabberd에 포함된 웹서버는 커스텀 모듈을 사용해 확장할 수 있습니다.

##### 튜토리얼

&#8220;/hello&#8221;로 시작하는 URL에 대한 모든 요청을 처리하기 위해 &#8220;mod\_http\_hello&#8221;를 작성한다고 가정해 봅시다, 다음과 같이:

  * http://localhost:5280/hello/world
  * http://localhost:5280/hello/hedgehog

process/2 함수의 절을 갖는 request 핸들러를 포함하는 &#8220;mod\_http\_hello.erl&#8221;파일을 작성하는 것으로 시작합니다.
  
&#8220;/hello/world&#8221; 요청을 처리하는 예는 다음과 같습니다:

```erlang
  
process(\_LocalPath = ["world"], \_Request) ->
      
{xmlelement, "html", [{"xmlns", "http://www.w3.org/1999/xhtml"}],
       
[{xmlelement, "head", [],
         
[{xmlelement, "title", [], []}]},
        
{xmlelement, "body", [],
         
[{xmlelement, "p", [], [{xmlcdata, "Hello, world!"}]}]}]};
  
```

> 모든 핸들러와 공통이기때문에, &#8220;hello&#8221; 파트는 여기에서 다루지 않습니다. 

&#8220;mod\_http\_hello.erl&#8221; 파일의 전체 소스는 다음과 같습니다:

```erlang
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
%%% File : mod\_http\_hello.erl
  
%%% Author : Your Name
  
%%% Purpose : Sample module that extends embedded ejabberd HTTP server
  
%%% Created :
  
%%% Id :
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

-module(mod\_http\_hello).
  
-author('your@email.org').
  
-vsn('').
  
-define(ejabberd_debug, true).

-behaviour(gen_mod).

-export([
     
start/2,
     
stop/1,
     
process/2
     
]).

-include("ejabberd.hrl").
  
-include("jlib.hrl").
  
-include("ejabberd_http.hrl").

%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
%%% REQUEST HANDLERS
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

process(["world"], _Request) ->
     
{xmlelement, "html", [{"xmlns", "http://www.w3.org/1999/xhtml"}],
      
[{xmlelement, "head", [],
        
[{xmlelement, "title", [], []}]},
       
{xmlelement, "body", [],
        
[{xmlelement, "p", [], [{xmlcdata, "Hello, world!"}]}]}]};

process(["produce\_error"], \_Request) ->
     
{400, [], {xmlelement, "h1", [],
                
[{xmlcdata, "400 Bad Request"}]}};

process(LocalPath, _Request) ->
     
{xmlelement, "html", [{"xmlns", "http://www.w3.org/1999/xhtml"}],
      
[{xmlelement, "head", [],
        
[{xmlelement, "title", [], []}]},
       
{xmlelement, "body", [],
        
[{xmlelement, "p", [], [{xmlcdata, io_lib:format("Called with path: ~p", [LocalPath])}]}]}]}.

%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
%%% BEHAVIOUR CALLBACKS
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

start(\_Host, \_Opts) ->
     
ok.

stop(_Host) ->
     
ok.
  
```

ejabberd가 시작할 때 mod\_http\_hello를 로드하기 위해 ejabberd.yml을 다음과 같이 수정합니다.

> 원문은 이전 버전의 ejabberd.cfg와 관련한 내용을 다루지만 번역자가 임의로 ejabberd.yml 관련 내용으로 변경했음을 알립니다. 

```
      
modules:
        
mod\_http\_hello: {}
  
```

"/hello/" 요청을 디스패치하기 위해 ejabberd.yml파일의 ejabberd\_http 모듈의 request\_handlers에 다음과 같이 mod\_http\_hello를 등록합니다.

```
      
port: 5280
      
module: ejabberd_http
        
request_handlers:
          
"/hello": mod\_http\_hello
      
web_admin: true
      
http_poll: true
  
```

##### API

gen_mod가 요구하는 콜백과는 별개로, 커스텀 모듈은 (하나 이상의 절을 갖는) process/2 함수를 구현해야 합니다:

  * your\_http\_module:process(LocalPath, Request)

process/2 함수는 HTTP 요청을 처리하며 클라이언트에 보낼 데이터를 반환합니다. 선택적으로 상태 코드와 추가 헤더를 포함하기도 합니다.

process/2 함수는 두개의 매개변수를 갖습니다: LocalPath와 Request

LocalPath는 "local to the module"이라 불리는 요청 URL의 일부분을 포함하는 리스트입니다.

예를 들어 mod_foo가 다음과 같이 설정되었다고 가정하면:

```
      
port: 5280
      
module: ejabberd_http
        
request_handlers:
          
"/a/b": mod_foo
      
web_admin: true
      
http_poll: true
  
```

유저의 요청 URL이 다음과 같을 때:

http://server:5280/a/b/c/d

"local to the moudle"은 다음과 같습니다:

```
      
["c", "d"]
  
```

일반적으로 서버 관리자는 그들이 선택한 path 접두사만 사용 가능하도록 모듈을 만들 수 있기 때문에, 여러분은 full path(Request#request.path) 대신에 local path 기반의 핸들러를 선택하고 싶을 것입니다.

Request 매개변수는 ejabberd_http.hrl에 정의된 HTTP request에 대한 정보를 포함하는 레코드입니다. 다음 필드로 구성됩니다:

```
      
{
        
"request",
        
method, %% HTTP method ("GET" or "POST")
        
path, %% Full path to requested resource
                   
%% e.g. for "http://server:5280/a/b/c/d": ["a", "b", "c", "d"]
        
q, %% Query part of the URL
                   
%% e.g. for "http://server:5280/a/b/c/d?foo=bar": [{"foo", "bar"}]
        
us, %% Authenticated user and server. Used in ejabberd\_web\_admin for now.
                   
%% e.g. for "foo@jabber.server.org": {"foo", "jabber.server.org"}
        
auth, %% Information provided for HTTP-auth (if any)
                   
%% e.g. for a user "john" who entered the password "secret": {"john", "secret"}
        
lang = "", %% Language code
        
data = "", %% POST data
      
}
  
```

### 내부 모듈을 외부 모듈처럼 실행하기

라우트 테이블만 사용하는 ejabberd 내부 모듈은 [epeios](https://blog.process-one.net/epeios_write_a_module_for_an_xmpp_server_once_run_it_everywhere/)와 함께 [XEP-0114](http://www.xmpp.org/extensions/xep-0114.html)를 준수하는 외부 모듈처럼 사용될 수 있으므로, 다른 XEP-0114와 호환되는 XMPP 서버들과 함께 사용될 수 있습니다.

## ejabberd 모듈 개발 시작

### ejabberd 설치 및 모듈 개발을 위한 준비

ejabberd는 /services/app/ejabberd에 설치되었으며,
  
ejabberd 소스는 /services/app/ejabberd/src/ejabberd-14.07 디렉토리에 있다고 가정합니다.

커스텀 모드파일 소스는 /services/app/ejabberd/src/ejabberd-14.07/src에 추가합니다.(이후 src로 통칭)

자세한 설치 관련 내용은 [ejabberd](ejabberd.md) 문서를 참조하세요.

### 개발

#### 스켈레톤 모듈

ejabberd의 모듈개발을 위한 뼈대 코드는 다음과 같습니다.

```erlang
  
-module(mod_foo).

-behavior(gen_mod).

-export([start/2, stop/1]).

start(\_Host, \_Opts) ->
      
ok.

stop(_Host) ->
      
ok.
  
```

#### 개발 목표

ejabberd의 [events and hooks](https://www.process-one.net/en/wiki/ejabberd_events_and_hooks/)방식을 사용해서 커스텀 모듈을 작성해 보겠습니다.
  
[Writing ejabberd modules: Presence Storms](http://metajack.im/2008/08/28/writing-ejabberd-modules-presence-storms/)문서를 참조해서 진행할 예정이며 모듈명은 mod_shunshine입니다.

##### 커스텀 모듈 추가

/services/app/ejabberd/etc/ejabberd/ejabberd.yml파일을 열어 modules: 밑에 다음과 같이 추가합니다.

```
      
modules:
          
mode_sunshine: {}
  
```

들여쓰기할 때 주의할 점은 tab을 사용하지 말고 공백 2문자를 사용해야 합니다.

##### mod_sunshine.erl 생성 및 간단한 로깅

src 디렉토리 밑에 mod_sunshine.erl파일을 생성한 후 다음과 같이 입력합니다.

```erlang
  
vim mod_sunshine.erl

-module(mod_sunshine).

-behavior(gen_mod).

-include("ejabberd.hrl").

-include("logger.hrl").

-export([start/2, stop/1, on_presence/4]).

start(Host, _Opts) ->
      
?INFO\_MSG("mod\_sunshine starting", []),
      
ok.

stop(Host) ->
      
?INFO\_MSG("mod\_sunshine stopping", []),
      
ok.
  
```

> 주의 : 참고 문서에는 logger.hrl이 include되지 않았지만 ejabberd 14.07에서는 logger.hrl을 포함해야 합니다. 

로깅을 위해 logger.hrl을 포함하고,
  
?INFO_MSG(format, args) 매크로를 사용해 info로그를 생성합니다.

로그파일에는 다음과 같이 출력됩니다.

```
      
17:23:19.860 [info] mod_sunshine starting
  
```

##### presence 후킹

유저의 presence stanza를 수신할 때를 가로채 봅시다.

```erlang
  
start(Host, _Opts) ->
      
?INFO\_MSG("mod\_sunshine starting", []),
      
ejabberd\_hooks:add(set\_presence\_hook, Host, ?MODULE, on\_presence, 50),
      
ok.

stop(Host) ->
      
?INFO\_MSG("mod\_sunshine stopping", []),
      
ejabberd\_hooks:delete(set\_presence\_hook, Host, ?MODULE, on\_presence, 50),
      
ok.

on\_presence(\_User, \_Server, \_Resource, _Packet) ->
      
none.
  
```

이벤트 후킹을 위해 ejabberd_hooks모듈을 사용하며 add와 delete로 이벤트 후킹시 콜백할 함수를 등록하거나 제거할 수 있습니다.

set\_presence\_hook은 presence 이벤트를 후킹하게 됩니다.

##### 옵션 처리

커스텀 모듈에 옵션(설정)값을 전달하는 방법을 설명합니다.

  * 우선 ejabberd.yml 파일을 다음과 같이 수정합니다.

```
        
mod_sunshine:
          
count: 10
          
interval: 60
  
```

> 주의: 들여쓰기는 tab을 사용하지 않고 반드시 공백으로 처리합니다. 

  * 소스 파일을 다음과 같이 수정합니다.

```erlang
  
-export([start/2, stop/1, on_presence/4]).

start(Host, _Opts) ->
      
?INFO\_MSG("mod\_sunshine starting", []),
      
ejabberd\_hooks:add(set\_presence\_hook, Host, ?MODULE, on\_presence, 50),
      
ok.

stop(Host) ->
      
?INFO\_MSG("mod\_sunshine stopping", []),
      
ejabberd\_hooks:delete(set\_presence\_hook, Host, ?MODULE, on\_presence, 50),
      
ok.

on\_presence(\_User, Server, \_Resource, \_Packet) ->
      
%% get options
      
StormCount = gen\_mod:get\_module_opt(Server, ?MODULE, count, 10),
      
TimeInterval = gen\_mod:get\_module_opt(Server, ?MODULE, interval, 60),
      
none.
  
```

on\_presence 함수에서 gen\_mod:get\_module\_opt()함수를 호출해 옵션을 가져옵니다.

  * 계속 작업 필요(erlang이 익숙해지면 후속 리서치 진행할 예정입니다.)

#### 컴파일

make && make install을 수행해서 변경된 소스만 배포합니다.

##### 컴파일 오류

  * p1_xml/include/xml.hrl이 없다는 오류는
  
    [링크](http://stackoverflow.com/questions/25407167/ejabberd-cant-find-include-lib-p1-xml-include)를 참고해서 다음과 같이 처리하면 됩니다.

ejabberd 소스 루트에서 디펜던시 라이브러리를 컴파일합니다.

    ./rebar get-deps
    ./rebar compile
    

그런 후 결과물을 다음과 같이 복사합니다.

     cp -R deps/* /services/app/ejabberd/lib/ejabberd/include/
    

### References

  * [ejabberd module development](http://stackoverflow.com/questions/22193106/how-to-develop-custom-functions-on-top-of-ejabberd)
  * [Writing ejabberd modules: Presence Stoms](http://metajack.im/2008/08/28/writing-ejabberd-modules-presence-storms/)
  * [writing ejabberd modules](http://sacharya.com/writing-ejabberd-modules/)
  * [XMPP bots ejabberd mod motion](http://happy.cat/blog/XMPP-Bots-ejabberd-mod-motion-2010-02-01-10-00.html)
  * [How to develop custom functions on top of ejabberd?](http://stackoverflow.com/questions/22193106/how-to-develop-custom-functions-on-top-of-ejabberd)