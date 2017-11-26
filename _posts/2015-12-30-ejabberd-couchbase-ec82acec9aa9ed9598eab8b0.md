---
id: 784
title: ejabberd couchbase 사용하기
date: 2015-12-30T17:32:45+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=784
permalink: '/2015/12/30/ejabberd-couchbase-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/'
categories:
  - couchbase
  - ejabberd
---
mongodb와 마찬가지로 couchbase역시 ejabberd에서 공식 지원하지 않는다. erlang driver도 커뮤니티에서 만든 driver만 제공하므로 사용상 주의할 필요가 있다.

couchbase의 erlang driver인 cberl을 사용해서 ejabberd용 couchbase 모듈을 만들어보자.

## 준비

  * erlang/otp 17(or 17.5)
  * ejabberd 15.04
  * couchbase server 4.x

cberl의 경우 빌드서버에는 libcouchbase2와 libcouchbase-dev가 설치되어 있어야 하며, 운영서버에는 libcouchbase2가 설치되어 있어야 한다.

```
  
sudo wget -O/etc/yum.repos.d/couchbase.repo http://packages.couchbase.com/rpm/couchbase-centos62-x86_64.repo
  
sudo yum check-update
  
sudo yum install &#8211;enablerepo=epel libcouchbase2 libcouchbase-devel
  
```

cberl의 경우 ejabberd에 연동시 cberl_drv.so파일을 찾는 경로가 priv/lib대신 priv로 되어 있어 오류가 발생한다.
  
이를 수정하기 위해 원본 리포지토리에서 fork해서 다음과 같이 생성해 놓았다.

https://github.com/jeidee/cberl

## ejabberd에 cberl 추가

다음과 같이 rebar.config.script파일을 수정한다.

```
  
Deps = [ &#8230;
          
{cberl, ".*", {git, "https://github.com/jeidee/cberl.git", "master"}},
  
```

## ejabberd용 couchbase 모듈 작성

cberl의 경우 내부적으로 poolboy모듈을 사용해 연결풀을 구현해 놓았기 때문에, ejabberd\_riak처럼 supervisor에서 gen\_server를 구현한 worker모듈을 풀링하는 방식으로 만들필요는 없다.

  * ejabberd_couchbase.erl
  
    단순히 couchbase의 api기능만 수행하도록 작성한다.</p> 
  * ejabberd\_couchbase\_sup.erl
  
    자식 워커 프로세스로 cberl을 생성해 주며, 슈퍼바이저 역할을 한다.
  
    ejabberd.yml에서 couchbase의 연결 설정 정보를 입력 받는다.

### ejabberd\_couchbase\_sup.erl

```erlang
  
-module(ejabberd\_couchbase\_sup).

%% API
  
-export([start/0,
      
start_link/0,
      
init/1,
      
get_pids/0,
      
transform_options/1,
      
get\_random\_pid/0,
      
get\_random\_pid/1
  
]).

-include("ejabberd.hrl").
  
-include("logger.hrl").

-define(DEFAULT\_POOL\_SIZE, 10).
  
-define(DEFAULT\_COUCHBASE\_START_INTERVAL, 30). % 30 seconds
  
-define(DEFAULT\_COUCHBASE\_HOST, "127.0.0.1").
  
-define(DEFAULT\_COUCHBASE\_PORT, 8091).
  
-define(DEFAULT\_COUCHBASE\_DATABASE, <>).

% time to wait for the supervisor to start its child before returning
  
% a timeout error to the request
  
-define(CONNECT_TIMEOUT, 500). % milliseconds

start() ->
      
case lists:any(
          
fun(Host) ->
              
is\_couchbase\_configured(Host)
          
end, ?MYHOSTS) of
          
true ->
              
do_start();
          
false ->
              
ok
      
end.

is\_couchbase\_configured(Host) ->
      
ServerConfigured = ejabberd\_config:get\_option(
          
{couchbase_server, Host},
          
fun(_) -> true end, false),
      
PortConfigured = ejabberd\_config:get\_option(
          
{couchbase_port, Host},
          
fun(_) -> true end, false),
      
ServerConfigured or PortConfigured.

do_start() ->
      
SupervisorName = ?MODULE,
      
ChildSpec =
          
{SupervisorName,
              
{?MODULE, start_link, []},
              
transient,
              
infinity,
              
supervisor,
              
[?MODULE]},
      
case supervisor:start\_child(ejabberd\_sup, ChildSpec) of
          
{ok, _PID} ->
              
ok;
          
_Error ->
              
?ERROR_MSG("Start of supervisor ~p failed:~n~p~nRetrying&#8230;~n",
                  
[SupervisorName, _Error]),
              
timer:sleep(5000),
              
start()
      
end.

start_link() ->
      
supervisor:start_link({local, ?MODULE}, ?MODULE, []).

init([]) ->
      
PoolSize = get\_pool\_size(),
      
\_StartInterval = get\_start_interval(),
      
Server = get\_couchbase\_server(),
      
Port = get\_couchbase\_port(),
      
Database = get\_couchbase\_database(),
      
User = "",
      
Pwd = "",
      
Host = get_host(Server, Port),
      
{ok, { {one\_for\_one, PoolSize*10, 1},
          
[{ejabberd_couchbase,
              
{cberl, start_link,
                  
[ejabberd\_couchbase, PoolSize, Host, User, Pwd, binary\_to_list(Database)]},
              
transient, 2000, worker, [?MODULE]}]

}}.

get\_start\_interval() ->
      
ejabberd\_config:get\_option(
          
couchbase\_start\_interval,
          
fun(N) when is_integer(N), N >= 1 -> N end,
          
?DEFAULT\_COUCHBASE\_START_INTERVAL).

get\_pool\_size() ->
      
ejabberd\_config:get\_option(
          
couchbase\_pool\_size,
          
fun(N) when is_integer(N), N >= 1 -> N end,
          
?DEFAULT\_POOL\_SIZE).

get\_couchbase\_server() ->
      
ejabberd\_config:get\_option(
          
couchbase_server,
          
fun(S) ->
              
binary\_to\_list(iolist\_to\_binary(S))
          
end, ?DEFAULT\_COUCHBASE\_HOST).

get\_couchbase\_port() ->
      
ejabberd\_config:get\_option(
          
couchbase_port,
          
fun(P) when is_integer(P), P > 0, P P end,
          
?DEFAULT\_COUCHBASE\_PORT).

get\_couchbase\_database() ->
      
ejabberd\_config:get\_option(
          
couchbase_database,
          
fun(S) ->
              
iolist\_to\_binary(S)
          
end, ?DEFAULT\_COUCHBASE\_DATABASE).

get_pids() ->
      
[ejabberd\_couchbase:get\_proc(I) || I
      
get\_random\_pid(now()).

get\_random\_pid(Term) ->
      
I = erlang:phash2(Term, get\_pool\_size()) + 1,
      
ejabberd\_couchbase:get\_proc(I).

transform_options(Opts) ->
      
lists:foldl(fun transform_options/2, [], Opts).

transform\_options({couchbase\_server, {S, P}}, Opts) ->
      
[{couchbase\_server, S}, {couchbase\_port, P}|Opts];
  
transform_options(Opt, Opts) ->
      
[Opt|Opts].

get_host(Server, Port) ->
      
binary\_to\_list(
          
iolist\_to\_binary(
              
[Server, ":", integer\_to\_list(Port)])).
  
```

  * init/1 함수에서 cberl을 자식프로세스로 생성해 준다.

```erlang
  
-module(ejabberd_couchbase).

%% API
  
-export([put/3, put/4,
      
remove/2, remove/1,
      
remove_all/1,
      
get/2,
      
view/3,
      
view\_from\_startkey/3]).

-include("ejabberd.hrl").
  
-include("logger.hrl").

%%%===================================================================
  
%%% API
  
%%%===================================================================

put(Table, Key, Value) ->
      
put(Table, Key, Value, 0).

put(Table, Key, Value, Expire) ->
      
TKey = atom\_to\_list(Table) ++ "::" ++ binary\_to\_list(Key),
      
case cberl:set(?MODULE, TKey, Expire, Value) of
          
ok ->
              
ok;
          
_ ->
              
{error, unknown_error}
      
end.

remove_all(Keys) ->
      
lists:foreach(
          
fun(Key) ->
              
TKey = proplists:get_value(<>, Key),
              
cberl:remove(?MODULE, TKey)
          
end, Keys),
      
ok.

remove(Table, Key) ->
      
TKey = atom\_to\_list(Table) ++ "::" ++ binary\_to\_list(Key),
      
cberl:remove(?MODULE, TKey).

remove(Key) ->
      
cberl:remove(?MODULE, Key).

get(Table, Key) ->
      
TKey = atom\_to\_list(Table) ++ "::" ++ binary\_to\_list(Key),
      
case cberl:get(?MODULE, TKey) of
          
{\_Key, \_Cas, Value} ->
              
{ok, Value};
          
{_Key, {error, Reason}} ->
              
{error, Reason};
          
_Err ->
              
{error, _Err}
      
end.

view(Document, View, Key) ->
      
Args = [{key, Key}],
      
case cberl:view(?MODULE, Document, View, Args) of
          
{ok, {_Length, Value}} ->
              
{ok, Value};
          
_ ->
              
{error, unknown_error}
      
end.

view\_from\_startkey(Document, View, StartKey) ->
      
Args = [{startkey, StartKey}],
      
case cberl:view(?MODULE, Document, View, Args) of
          
{ok, {_Length, Value}} ->
              
{ok, Value};
          
_ ->
              
{error, unknown_error}
      
end.

```

ejabberd_couchbase.erl 모듈은 couchbase의 API 모듈 역할을 수행한다.

### ejabberd에서 ejabberd_couchbase 모듈 사용

ejabberd_app.erl의 start/2 함수에 다음행을 추가한다.

```erlang
      
ejabberd\_couchbase\_sup:start(),
  
```

## 참고

  * [cberl](https://github.com/jeidee/cberl)