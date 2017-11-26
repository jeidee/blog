---
id: 20
title: ejabberd 외부 인증 모듈과 http 커스텀 모듈 연동
date: 2015-01-21T23:38:30+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/ejabberd-%ec%99%b8%eb%b6%80-%ec%9d%b8%ec%a6%9d-%eb%aa%a8%eb%93%88%ea%b3%bc-http-%ec%bb%a4%ec%8a%a4%ed%85%80-%eb%aa%a8%eb%93%88-%ec%97%b0%eb%8f%99/'
permalink: '/2015/01/21/ejabberd-%ec%99%b8%eb%b6%80-%ec%9d%b8%ec%a6%9d-%eb%aa%a8%eb%93%88%ea%b3%bc-http-%ec%bb%a4%ec%8a%a4%ed%85%80-%eb%aa%a8%eb%93%88-%ec%97%b0%eb%8f%99/'
categories:
  - ejabberd
---
ejabberd의 외부 인증 모듈을 사용해서 인증 처리를 할 경우, 기존 멤버쉽과 연동하는 방법을 찾아 보도록 한다.

우선 들어가기에 앞서 작업 개요를 살펴보면 다음과 같다.

  * python으로 외부 인증 모듈 작성
  * ejabberd를 외부에서 제어할 수 있도록 mod\_http\_api.erl 을 작성해 커스텀 모듈 등록
  * mod\_http\_api에서 기존 멤버쉽의 회원 정보 조회(편의상 internal rest api가 존재한다고 가정한다.)

다음과 같은 로그인 처리 시나리오를 가정해 보자.

  1. 로그인 할 경우 외부 인증 모듈에서 http://localhost:5280/api/auth?user=romeo&server=localhost&password=1234와 같은 형식으로 호출한다.
  2. mod\_http\_api에서 ejabberd 모듈을 사용해 유저를 로그인 처리한다.
  3. ejabberd에 없을 경우, 멤버쉽에서 해당 유저를 조회한다. 
      1. 멤버쉽에 해당 유저가 있을 경우, 가져와서 ejabberd에 등록한다.
      2. 멤버쉽에 해당 유저가 없다면 로그인 실패한다.
  4. ejabberd에 있을 경우, 암호를 확인해 로그인 처리한다. 

## mod\_http\_api.erl 작성

### 기본 골격 코드 작성

먼저 기본 골격 코드는 다음과 같다.

```erlang
      
-module(mod\_http\_api).
      
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
      
-include("logger.hrl").
      
-include("http_bind.hrl").

%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
      
%%% REQUEST HANDLERS
      
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

process([<>], _Request) ->
          
?DEBUG("Hello Request: ~p", [_Request]),
          
{200, ?HEADER,
           
#xmlel{name = <>, children = [{xmlcdata, <>}]}}.

process(\_Path, \_Request) ->
          
?DEBUG("Bad Request: ~p, ~p", [\_Path, \_Request]),
          
{400, ?HEADER,
           
#xmlel{name = <>, children = [{xmlcdata, <>}]}}.

%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
      
%%% BEHAVIOUR CALLBACKS
      
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

start(\_Host, \_Opts) ->
          
ok.

stop(_Host) ->
          
ok.
  
```

ejabberd/src/ 밑에 mod\_http\_api.erl 파일명으로 저장한다.

위와 같이 작성한 후 ejabberd.yml을 다음과 같이 수정한다.

```erlang
      
listen:
        
&#8230;(중간 생략)
        
request_handlers:
          
"/api": mod\_http\_api

&#8230;(중간 생략)

modules:
        
&#8230;(중간 생략)
        
mod_version: {}
        
mod\_http\_api: {}
  
```

make & make install을 사용해서 다시 빌드한 후 ejabberd를 기동한다.
  
웹브라우저를 띄우고 http://localhost:5280/api/hello(편의상 이후에는 /api/hello와 같이 줄여서 사용함)에 접속하면 &#8220;Hello World~!!!&#8221; XML 문자열이 브라우저에 출력된다.

여기까지 무사히 진행했다면 본격적으로 시작해 보자.

### auth api 작성

/api/auth?user=romeo&server=localhost&password=1234와 같은 api 호출을 받아드리도록 process/2 함수를 확장한다.

```erlang
      
process([<>], _Request) ->
          
Args = element(4, _Request),
          
User = proplists:get_value(<>, Args),
          
Server = proplists:get_value(<>, Args),
          
Pwd = proplists:get_value(<>, Args),
          
IsExists = ejabberd\_auth\_internal:is\_user\_exists(User, Server),
          
Result = to\_bin("~p ~n user=~p, server=~p, pwd=~p is\_exists=~p ~n", [_Request, User, Server, Pwd, IsExists]),
          
?DEBUG("Auth Request: ~p", [Args]),
          
{200, ?HEADER,Result};

to_bin(Format, List) ->
          
binary:list\_to\_bin(io_lib:format(Format, List)).
  
```

> to_bin/2 함수는 유틸 함수로 편의상 임의로 작성한 함수이다. 

_Request를 출력해보면 다음과 같은 튜플을 확인할 수 있다.

```erlang
      
{request,'GET',
               
[<>,<>],
               
[{<>,<>},
                
{<>,<>},
                
{<>,<>}],
               
{<>,<>},
               
{<>,<>},
               
<>,<>,
               
{ {10,0,2,2},53260},
               
<>,5280,http,
               
[{'Accept-Language',<>},
                
{'Accept-Encoding',<>},
                
{'User-Agent',<>},
                
{'Accept',<>},
                
{'Authorization',<>},
                
{'Cache-Control',<>},
                
{'Connection',<>},
                
{'Host',<>}]}
  
```

위의 튜플 요소 중에 중요한 것은 네 번째 요소인 query string을 파싱한 결과인 파라미터 튜플 리스트이다.

```erlang
      
[{<>,<>},
       
{<>,<>},
       
{<>,<>}]
  
```

_Request에서 해당 요소만 추출하기 위해서 element/2 함수를 다음과 같이 사용한다.

```erlang
      
Args = element(4, _Request),
  
```

Args에 원하는 파라미터 튜플 리스트가 매치되고, Args에서 "user", "server", "password" 파라미터에 해당하는 값을 다음과 같이 추출할 수 있다.

```erlang
      
User = proplists:get_value(<>, Args),
      
Server = proplists:get_value(<>, Args),
      
Pwd = proplists:get_value(<>, Args),
  
```

proplists 모듈의 get_value/2 함수를 사용하면 튜플 리스트에서 key/value로 값을 조회할 수 있다.

위의 과정을 거치면 User, Server, Pwd 변수에 각각 <<"romeo">>, <<"localhost">>, <<"1234">> 바이너리 리스트가 할당된다.

이제 이 값을 가지고 이후의 과정을 처리하면 된다.

#### ejabberd 유저 체크

해당 유저가 ejabberd에 등록된 유저인지 확인해 보자.
  
다음과 같이 ejabberd\_auth\_internal 모듈의 is\_user\_exists/2 함수를 사용한다.

```erlang
      
IsExists = ejabberd\_auth\_internal:is\_user\_exists(User, Server),
  
```

user가 존재한다면 IsExists 변수에는 true가, 아닐 경우 false가 바운드된다.

**ejabberd에 등록되지 않은 유저일 경우**

이 부분 이후는 다음 글에서 이어서 작성하도록 한다.