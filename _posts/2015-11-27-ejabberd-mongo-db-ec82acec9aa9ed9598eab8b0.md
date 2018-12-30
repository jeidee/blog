---
author: jeidee
categories:
- ejabberd
date: '2015-11-27'
guid: https://erlnote.wordpress.com/?p=622
id: 622
permalink: /2015/11/27/ejabberd-mongo-db-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/
tags:
- mongo
- mongo-db
title: ejabberd mongo-db 사용하기
url: /2015/11/27/ejabberd-mongo-db-ec82acec9aa9ed9598eab8b0
---

ejabberd에는 mongodb를 사용할 수 있는 모듈이 없다.
  
mongodb에서도 erlang driver를 공식적으로 지원하지 않으며, community driver(일반 개발자가 만들어 공개한)만 사용할 수 있다.

community erlang driver중에서 [mongodb-erlang](https://github.com/comtihon/mongodb-erlang)을 사용해 ejabberd용 mongodb 모듈을 만들어 보자.

## 준비

  * erlang/otp 17(or 17.5)
  * ejabberd 15.04
  * mongodb 3.0.7

[mongodb-erlang](https://github.com/comtihon/mongodb-erlang)의 경우 의존하고 있는 라이브러리 중에서 [pbkdf2](https://github.com/basho/erlang-pbkdf2.git)가 최신 버전의 erlang(17~)에서 컴파일이 안되는 문제(rebar.config에 명시된 {require\_min\_otp_vsn, &#8220;R14B03&#8221;}. 때문)가 있고, [bson-erlang](//github.com/comtihon/bson-erlang) 역시 동일한 문제가 있어, 이를 수정해서 다음과 같이 fork를 생성했다.

  * [jeidee/mongodb-erlang](https://github.com/jeidee/mongodb-erlang)

## ejabberd에 mongodb-erlang 추가

다음과 같이 rebar.config.script파일을 수정한다.

```
  
Deps = [ &#8230;
          
{mongodb, &quot;.*&quot;, {git, &quot;https://github.com/jeidee/mongodb-erlang.git&quot;, &quot;master&quot;}},
  
&#8230;
  
```

빌드해서 실행하면 mongodb-erlang 모듈을 사용할 수 있게 된다.

## ejabberd용 mongodb 모듈 작성

기본 구조는 ejabberd\_riak/ejabberd\_riak_sup 모듈을 따른다.

  * ejabberd_mongodb.erl
  
    gen_server를 구현하며, 하나의 서버 프로세스당 mongodb에 하나의 연결을 갖는다.
  
    put/get/delete/update API를 노출하며, 각각의 operation은 connection pool을 사용해 처리된다.
  * ejabberd\_mongodb\_sup.erl
  
    ejabberd_mongodb 프로세스를 connection pool size만큼 생성하며, supervisor로서 해당 프로세스를 관리한다.
  
    ejabberd 설정파일(ejabberd.yml)에서 mongodb의 연결 설정 정보를 입력 받는다.

### ejabberd\_mongodb\_sup.erl

```erlang
  
-module(ejabberd\_mongodb\_sup).

%% API
  
-export([start/0,
      
start_link/0,
      
init/1,
      
get_pids/0,
      
transform_options/1,
      
get\_random\_pid/0,
      
get\_random\_pid/1
  
]).

-include(&quot;ejabberd.hrl&quot;).
  
-include(&quot;logger.hrl&quot;).

-define(DEFAULT\_POOL\_SIZE, 10).
  
-define(DEFAULT\_MONGODB\_START_INTERVAL, 30). % 30 seconds
  
-define(DEFAULT\_MONGODB\_HOST, &quot;127.0.0.1&quot;).
  
-define(DEFAULT\_MONGODB\_PORT, 27017).
  
-define(DEFAULT\_MONGODB\_DATABASE, <<&quot;ejabberd&quot;>>).

% time to wait for the supervisor to start its child before returning
  
% a timeout error to the request
  
-define(CONNECT_TIMEOUT, 500). % milliseconds

start() ->
      
case lists:any(
          
fun(Host) ->
              
is\_mongo\_configured(Host)
          
end, ?MYHOSTS) of
          
true ->
              
ejabberd:start_app(bson),
              
ejabberd:start_app(crypto),
              
ejabberd:start_app(mongodb),
              
do_start();
          
false ->
              
ok
      
end.

is\_mongo\_configured(Host) ->
      
ServerConfigured = ejabberd\_config:get\_option(
          
{mongo_server, Host},
          
fun(_) -> true end, false),
      
PortConfigured = ejabberd\_config:get\_option(
          
{mongo_port, Host},
          
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
              
?ERROR_MSG(&quot;Start of supervisor ~p failed:~n~p~nRetrying&#8230;~n&quot;,
                  
[SupervisorName, _Error]),
              
timer:sleep(5000),
              
start()
      
end.

start_link() ->
      
supervisor:start_link({local, ?MODULE}, ?MODULE, []).

init([]) ->
      
PoolSize = get\_pool\_size(),
      
StartInterval = get\_start\_interval(),
      
Server = get\_mongo\_server(),
      
Port = get\_mongo\_port(),
      
Database = get\_mongo\_database(),
      
{ok, { {one\_for\_one, PoolSize*10, 1},
          
lists:map(
              
fun(I) ->
                  
{ejabberd\_mongodb:get\_proc(I),
                      
{ejabberd\_mongodb, start\_link,
                          
[I, Server, Port, Database, StartInterval*1000]},
                      
transient, 2000, worker, [?MODULE]}
              
end, lists:seq(1, PoolSize))}}.

get\_start\_interval() ->
      
ejabberd\_config:get\_option(
          
mongo\_start\_interval,
          
fun(N) when is_integer(N), N >= 1 -> N end,
          
?DEFAULT\_MONGODB\_START_INTERVAL).

get\_pool\_size() ->
      
ejabberd\_config:get\_option(
          
mongo\_pool\_size,
          
fun(N) when is_integer(N), N >= 1 -> N end,
          
?DEFAULT\_POOL\_SIZE).

get\_mongo\_server() ->
      
ejabberd\_config:get\_option(
          
mongo_server,
          
fun(S) ->
              
binary\_to\_list(iolist\_to\_binary(S))
          
end, ?DEFAULT\_MONGODB\_HOST).

get\_mongo\_port() ->
      
ejabberd\_config:get\_option(
          
mongo_port,
          
fun(P) when is_integer(P), P > 0, P P end,
          
?DEFAULT\_MONGODB\_PORT).

get\_mongo\_database() ->
      
ejabberd\_config:get\_option(
          
mongo_database,
          
fun(S) ->
              
iolist\_to\_binary(S)
          
end, ?DEFAULT\_MONGODB\_DATABASE).

get_pids() ->
      
[ejabberd\_mongodb:get\_proc(I) || I
      
get\_random\_pid(now()).

get\_random\_pid(Term) ->
      
I = erlang:phash2(Term, get\_pool\_size()) + 1,
      
ejabberd\_mongodb:get\_proc(I).

transform_options(Opts) ->
      
lists:foldl(fun transform_options/2, [], Opts).

transform\_options({mongo\_server, {S, P}}, Opts) ->
      
[{mongo\_server, S}, {mongo\_port, P}|Opts];
  
transform_options(Opt, Opts) ->
      
[Opt|Opts].
  
```

중요한 부분은 함수는 다음과 같다.

  * init/1 : mongodb 연결정보를 설정에서 읽어 연결 풀 사이즈만큼 ejabberd_mongodb 프로세스를 생성한다.</p> 
  * start/0 : mongodb-erlang 모듈에서 필요로 하는 bson/crypto application을 시작하고 mongodb application을 시작한다.</p> 

### ejabberd_mongodb.erl

```erlang
  
-module(ejabberd_mongodb).
  
-behaviour(gen_server).

%% API
  
-export([start\_link/5, get\_proc/1, make_collection/1,
      
put/2, put/3,
      
get\_one/2, get\_all/2, delete\_one/2, delete\_all/2,
      
update/3,
      
create\_index/2, create\_index/3, create\_index/4, create\_index/5,
      
is_connected/0]).

%% gen_server callbacks
  
-export([init/1, handle\_call/3, handle\_cast/2, handle_info/2,
      
terminate/2, code_change/3]).

-include(&quot;ejabberd.hrl&quot;).
  
-include(&quot;logger.hrl&quot;).

-record(state, {pid = self() :: pid()}).

%%%===================================================================
  
%%% API
  
%%%===================================================================
  
%% @private
  
start\_link(Num, Server, Port, Database, \_StartInterval) ->
      
gen\_server:start\_link({local, get_proc(Num)}, ?MODULE, [Server, Port, Database], []).

%% @private
  
is_connected() ->
      
is\_process\_alive(get\_random\_pid()).

%% @private
  
get_proc(I) ->
      
jlib:binary\_to\_atom(
          
iolist\_to\_binary(
              
[atom\_to\_list(?MODULE), $\_, integer\_to_list(I)])).

-spec make_collection(atom()) -> binary().
  
%% @doc Makes a bucket from a table name
  
%% @private
  
make_collection(Table) ->
      
erlang:atom\_to\_binary(Table, utf8).

%% Doc example) #{}, [#{}], [{}]
  
put(Table, Doc) ->
      
Connection = get\_random\_pid(),
      
Collection = make_collection(Table),
      
mongo:insert(Connection, Collection, Doc).

put(Table, Key, Value) ->
      
Connection = get\_random\_pid(),
      
Collection = make_collection(Table),
      
mongo:insert(Connection, Collection, [{Key, Value}]).

%% selector exaple) {<>, {'$gt', 1}, <>, {'$lt', 3}}
  
get_one(Table, Selector) ->
      
Connection = get\_random\_pid(),
      
Collection = make_collection(Table),
      
mongo:find_one(Connection, Collection, Selector).

get_all(Table, Selector) ->
      
Connection = get\_random\_pid(),
      
Collection = make_collection(Table),
      
Cursor = mongo:find(Connection, Collection, Selector),
      
Result = mc_cursor:rest(Cursor),
      
mc_cursor:close(Cursor),
      
Result.

delete_one(Table, Selector) ->
      
Connection = get\_random\_pid(),
      
Collection = make_collection(Table),
      
mongo:delete_one(Connection, Collection, Selector).

delete_all(Table, Selector) ->
      
Connection = get\_random\_pid(),
      
Collection = make_collection(Table),
      
mongo:delete(Connection, Collection, Selector).

%% Doc example) {<>, <>, <>, 10}
  
update(Table, Selector, Doc) ->
      
Connection = get\_random\_pid(),
      
Collection = make_collection(Table),
      
mongo:update(Connection, Collection, Selector, {<>, Doc}).

%% IndexSpec example) #{<> => {<>, 1}, <> => <>, <> => false, <> => false}
  
%% key {field, 1, other, -1, location, 2d}
  
create_index(Table, IndexColumn) ->
      
create_index(Table, IndexColumn, IndexColumn).

create_index(Table, IndexColumn, IndexName) ->
      
create_index(Table, IndexColumn, IndexName, false).

create_index(Table, IndexColumn, IndexName, Unique) ->
      
create_index(Table, IndexColumn, IndexName, Unique, false).

create_index(Table, IndexColumn, IndexName, Unique, DropDups) ->
      
Connection = get\_random\_pid(),
      
Collection = make_collection(Table),
      
mongo:ensure_index(Connection, Collection, #{<> => {IndexColumn, 1}, <> => IndexName, <> => Unique, <> => DropDups}).

%%%===================================================================
  
%%% gen_server API
  
%%%===================================================================
  
%% @private
  
init([Server, Port, Database]) ->
      
case mongo:connect([{database, Database}, {host, Server}, {port, Port}]) of
          
{ok, Pid} ->
              
erlang:monitor(process, Pid),
              
{ok, #state{pid = Pid}};
          
Err ->
              
{stop, Err}
      
end.

%% @private
  
handle\_call(get\_pid, _From, #state{pid = Pid} = State) ->
      
{reply, {ok, Pid}, State};
  
handle\_call(\_Request, _From, State) ->
      
Reply = ok,
      
{reply, Reply, State}.

%% @private
  
handle\_cast(\_Msg, State) ->
      
{noreply, State}.

%% @private
  
handle\_info({'DOWN', \_MonitorRef, \_Type, \_Object, _Info}, State) ->
      
?ERROR\_MSG(&quot;down info: ~p&quot;, [\_Info]),
      
{stop, normal, State};
  
handle\_info(\_Info, State) ->
      
?ERROR\_MSG(&quot;unexpected info: ~p&quot;, [\_Info]),
      
{noreply, State}.

%% @private
  
terminate(\_Reason, \_State) ->
      
ok.

%% @private
  
code\_change(\_OldVsn, State, _Extra) ->
      
{ok, State}.

%%%===================================================================
  
%%% Internal functions
  
%%%===================================================================

get\_random\_pid() ->
      
PoolPid = ejabberd\_mongodb\_sup:get\_random\_pid(),
      
case catch gen\_server:call(PoolPid, get\_pid) of
          
{ok, Pid} ->
              
Pid;
          
{'EXIT', {timeout, _}} ->
              
throw({error, timeout});
          
{'EXIT', Err} ->
              
throw({error, Err})
      
end.

```

어려운 코드는 없다.
  
mongo모듈의 find/update/delete 함수 사용시 쿼리 매개변수로 사용하는 Selector와 Doc의 경우 설명이 필요한데 다음과 같다.

  * Selector
  
    map type이며 #{Key => Value, Key => Value}나, {Key, Value, Key, Value&#8230;}와 같이 작성한다.
  
    Key는 필드명이며 바이너리 타입을 갖는다.
  
    Value는 mongodb의 query criteria 형태를 입력한다.</p> 
  * Doc
  
    map type이며 Value를 map type으로 지정할 수 있다.

  * IndexSpec
  
    mongodb에 인덱스를 생성하기 위해서는 인덱스의 구조를 정의해야 한다.
  
    IndexSpec은 인덱스의 구조를 정의하며 key, name, unique, dropDups 필드를 갖는다. (각각이 의미하는 바는 mongodb 문서 참고)

### ejabberd에서 ejabberd_mongo 모듈 사용

ejabberd_app.erl의 start/2 함수에 다음행을 추가한다.

```erlang
      
ejabberd\_mongodb\_sup:start(),
  
```

이로써 ejabberd에서 mongodb를 사용할 수 있는 준비는 모두 되었다.
  
(* mongodb에 인증이 필요한 경우 인증 정보(user, password)를 추가해 주어야 한다. => 추후 갱신)

## 최종 테스트

ejabberd를 빌드/실행한 후 다음과 같이 테스트해 보자.

```
  
$ ejabberdctl start
  
$ ejabberdctl debug

> ejabberd_mongodb:put(test, <<&quot;name&quot;>>, <<&quot;Hong Gil Dong&quot;>>).
  
> ejabberd\_mongodb:get\_one(test, <<&quot;name&quot;>>, <<&quot;Hong Gil Dong&quot;>>).
  
```

## 참고

  * [mongodb erlang driver](https://docs.mongodb.org/ecosystem/drivers/erlang/)