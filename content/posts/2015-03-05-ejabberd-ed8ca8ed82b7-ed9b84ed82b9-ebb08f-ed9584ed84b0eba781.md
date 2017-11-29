---
author: jeidee
categories:
- ejabberd
date: '2015-03-05'
guid: https://erlnote.wordpress.com/?p=272
id: 272
permalink: /2015/03/05/ejabberd-%ed%8c%a8%ed%82%b7-%ed%9b%84%ed%82%b9-%eb%b0%8f-%ed%95%84%ed%84%b0%eb%a7%81/
tags:
- hook
title: ejabberd 패킷 후킹 및 필터링
url: /2015/03/05/ejabberd-ed8ca8ed82b7-ed9b84ed82b9-ebb08f-ed9584ed84b0eba781
---

ejabberd에서 패킷을 후킹한 후 필터링하고 싶다면 다음과 같이 커스텀 모듈을 생성해 처리하면 된다.

다음 코드는 chat 메세지 패킷을 가로체 디버그 로그에 출력해 주는 소스이다.

```erlang
  
-module(mod\_packet\_hook).

-behaviour(gen_mod).

-export([start/2,
      
stop/1]).
  
-export([on\_filter\_packet/1]).

-include("ejabberd.hrl").
  
-include("jlib.hrl").
  
-include("logger.hrl").

start(\_Host, \_Opts) ->
      
ejabberd\_hooks:add(filter\_packet, global, ?MODULE, on\_filter\_packet, 0),
      
ok.

stop(_Host) ->
      
ejabberd\_hooks:delete(filter\_packet, global, ?MODULE, on\_filter\_packet, 0),
      
ok.

on\_filter\_packet({From, To, XML} = Packet) ->
      
%% does something with a packet
      
%% should return modified Packet or atom \`drop\` to drop the packet
      
Type = proplists:get_value(<<"type">>, XML#xmlel.attrs),
      
if
          
XML#xmlel.name =:= <<"message">>,
           
Type =:= <<"chat">>; Type =:= <<"groupchat">> ->
              
lists:foreach(fun(Item) ->
                  
if
                      
Item#xmlel.name =:= <<"body">> ->
                          
lists:foreach(fun(Data) ->
                              
{xmlcdata, Msg} = Data,
                              
?DEBUG("Msg is ~p~n", [Msg])
                          
end, Item#xmlel.children);
                      
true ->
                          
ok
                  
end
              
end, XML#xmlel.children);
          
true ->
              
ok
      
end,
      
?DEBUG("Packet:~p~n~nFrom:~p~nTo:~p~nXML:~p~n", [Packet, From, To, XML]),
      
Packet. %드롭하고 싶다면 drop atom을 반환한다.
  
```

중요한 부분은 다음이다.

```erlang
      
ejabberd\_hooks:add(filter\_packet, global, ?MODULE, on\_filter\_packet, 0),
  
```

filter\_packet이벤트를 모든 호스트(global)에 걸쳐 등록하고 핸들러를 on\_filter_packet로 등록한다.

## 참고

  * [How to filter messages in Ejabberd](http://stackoverflow.com/questions/1939879/how-to-filter-messages-in-ejabberd)