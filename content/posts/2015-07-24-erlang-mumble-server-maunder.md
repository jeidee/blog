---
author: jeidee
categories:
- erlang
date: '2015-07-24'
guid: https://erlnote.wordpress.com/?p=554
id: 554
permalink: /2015/07/24/erlang-mumble-server-maunder/
tags:
- maunder
- mumble
title: erlang mumble server &#8211; maunder
url: /2015/07/24/erlang-mumble-server-maunder
---

erlang으로 구현된 mumble서버 중에 [maunder](https://github.com/tcoram/maunder)라고 있다.

2년전 커밋이 마지막이다.
  
erlang 17버전에서 빌드하고 실행하면 application:start()할 때 오류가 발생한다.

원인은 erlang 17버전에서 public_key application이 asn1을 의존하기 때문인데,
  
maunder.erl 소스를 다음과 같이 수정하고 다시 빌드하면 문제가 해결된다.

```
  
%% @spec start() -> ok
  
%% @doc Start the maunder server.
  
start() ->
      
ensure_started(sasl),
      
ensure_started(crypto),
      
ensure_started(asn1),
      
ensure\_started(public\_key),
      
ensure_started(ssl),
      
application:start(maunder).
  
```

기동시 eaddrinuse 관련 에러가 발생하는 경우,
  
maunder\_app.erl의 DEF\_PORT값(기본값은 8080)을 변경한 후 다시 빌드하면 된다.

매번 포트를 변경할 때마다 재빌드하는게 번거로울 경우 빌드후 생성되는 ebin/maunder.app에 listen_port 설정을 다음과 같이 추가하면 된다.

```
                
{env,[{version,{1,<<"maunder">>,<<"linux">>,<<"erlangish">>}},
                      
{listen_port,19980},
  
```

하지만 소스상의 문제인지 erlang이 버전업하면서 생긴 문제인지 maunder\_app.erl의 get\_app_env/2 함수가 제대로 동작하지 않는다.
  
정확하게 말해 application:get_application()의 값이 undefined로 반환되기 때문인데,
  
이 부분은 추후 파악되면 업데이트하기로 하겠다.

임시 방편으로 소스를 다음과 같이 수정하면 된다.

```
  
get\_app\_env(Opt, Default) ->
  
%% case application:get\_env(application:get\_application(), Opt) of
      
case application:get_env(maunder, Opt) of
  
```

매번 빌드할 때 마다 maunder.app.src의 내용으로 ebin/maunder.app 파일 내용이 교체되므로 주의가 필요하다.