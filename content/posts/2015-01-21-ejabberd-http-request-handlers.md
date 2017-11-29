---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-http-request-handlers/
id: 29
permalink: /2015/01/21/ejabberd-http-request-handlers/
tags:
- custom module
- http
title: ejabberd http request handlers
url: /2015/01/21/ejabberd-http-request-handlers
---

[ejabberd 모듈 개발 문서](https://erlnote.wordpress.com/2015/01/21/ejabberd-%EB%AA%A8%EB%93%88-%EA%B0%9C%EB%B0%9C/) 중에 http request handlers 개발 관련해서 간단히 정리해 봅니다.

## 튜토리얼

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

## API

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