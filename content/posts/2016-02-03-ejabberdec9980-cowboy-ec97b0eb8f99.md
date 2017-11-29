---
author: jeidee
categories:
- ejabberd
date: '2016-02-03'
guid: https://erlnote.wordpress.com/?p=844
id: 844
permalink: /2016/02/03/ejabberd%ec%99%80-cowboy-%ec%97%b0%eb%8f%99/
tags:
- cowboy
title: ejabberd와 cowboy 연동
url: /2016/02/03/ejabberdec9980-cowboy-ec97b0eb8f99
---

ejabberd에 포함된 관리자용 웹사이트는 인터페이스가 썩 편하지 않다. 더군다나 admin 모듈(ejabberd\_web\_admin.erl)을 살펴보면 erlang 코드에서 직접 html을 생성하도록 코드가 작성되어 있어 수정하기가 여간 불편한게 아니다.

이러한 이유로 erlang web framework인 cowboy를 연동하기로 했다.

cowboy는 현재 erlang/otp 18에서 컴파일 가능하기 때문에 다음과 같이 준비가 되어 있어야 한다.

## 준비

  * erlang/otp 18
  * ejabberd 15.04

ejabberd의 최신 버전의 경우 erlang/otp 18을 정상 지원하지만, 현재 사용하고 있는 15.04의 경우 일부 모듈(특히 ejabberd_riak관련 의존성 모듈들)은 erlang/otp 18에서 컴파일 되지 않는 문제가 있다.

이러한 문제를 피하고자 할 경우 riak를 사용하지 않거나 최신 버전의 ejabberd를 사용하면 된다.

## ejabberd에 cowboy 추가

다음과 같이 rebar.config.script파일을 수정한다.

```
  
Deps = [ &#8230;
          
{cowboy, ".*", {git, "https://github.com/ninenines/cowboy.git", "master"}},
  
```

### 정적 html파일 처리

  * 정적 html파일은 ejabberd의 priv 디렉토리 밑에 html 디렉토리를 생성한 후 추가한다.
  * Makefile.in 파일을 다음과 같이 수정한다.

```
  
\# /usr/lib/ejabberd/priv/html
  
HTMLDIR = $(PRIVDIR)/html

\# Copy html
  
$(INSTALL) -d $(HTMLDIR) && cp -R priv/html $(PRIVDIR)
  
#
  
```

## ejabberd용 cowboy 모듈 작성

ejabberd 전용 플러그인 모듈(gen_mod를 구현하는) 형태로 작성할 것이다.

  * mod_webadmin.erl
  
    gen_mod behaviour를 구현하며, 시작 시 cowboy의 http서버를 시작한다.
  
    http서버를 시작할 때 시작 매개변수로 http 요청을 라우팅할 dispatch를 작성해서 전달해야 한다.</p> 
  * mod\_webadmin\_api_handler.erl
  
    /api/로 시작하는 요청을 처리하는 모듈이다.

  * mod\_webadmin\_sm.erl
  
    관리웹사이트의 세션을 관리하기 위한 모듈이다. 데이터베이스는 mnesia를 사용한다.

### mod_webadmin.erl

https://gist.github.com/jeidee/55321c1a3786c116c023

  * check_session/1 함수는 dispatch 모듈에서 인증이 필요한 request마다 인증쿠키를 확인하는 역할을 수행한다.
  * cowboy 모듈은 ranch, cowlib 모듈을 의존하므로 start/2 함수에서 cowboy를 시작하기 전에 반드시 먼저 시작해 주어야 한다.
  * start/2 함수는 ejabberd에 의해 호출되며, start/2 함수에서 do_start/1 함수를 호출한다.
  
    do\_start/1 함수에서 cowboy:start\_http/4 함수를 호출해 웹서버를 시작한다.
  * Dispatch = cowboy_router:compile([ &#8230; 부분이 중요하다.
  
    이 부분에서 요청 경로별 처리할 모듈을 배정하는데, &#8220;/[&#8230;]&#8221;과 &#8220;/&#8221;의 차이는, 전자의 경우 모든 하위 경로를 처리한다는 것이고, 후자의 경우 하위 경로를 제외하고 지정된 경로의 요청만 처리한다는 의미이다.

중요한 것은 순서인데, &#8220;/&#8221;와 &#8220;/[&#8230;]&#8221;의 순서를 바꿀 경우 &#8220;/&#8221;의 처리는 절대 되지 않음을 주의해야 한다.
  
* https 웹서버를 시작하는 코드를 주석처리해 놓았다. 필요할 경우 주석을 제외하고 사용하면 된다.

### mod\_webadmin\_api_handler.erl

https://gist.github.com/jeidee/66169e51541be6d64b87

  * init/2의 Req와 Opts 매개변수는 cowboy에 의해 전달된다. Opts의 경우 cowboy:start\_http/4 함수에 전달된 dispatch 모듈 지정 시 할당된 값이 전달된다. ({&#8220;/api/[&#8230;]&#8221;, mod\_webadmin\_api\_handler, [Host]}, 여기에서 Host값이 전달된다.)</p> 
  * 포인트는 Req에서 Method와 PathInfo를 추출해 api/4 함수에 매치하는 방법으로 경로별 라우팅을 구현한 것이다.

  * 로그인/로그아웃/인증확인을 위한 코드를 유심히 살펴보도록 하자.

### mod\_webadmin\_sm.erl

https://gist.github.com/jeidee/5fd86b5c9ac023c81256

  * 세션을 관리하는 모듈이다. otp의 gen_server를 구현하며 mnesia의 ram copy 테이블을 사용해서 세션정보를 관리한다.

  * login/2 함수 에서 사용한 acl은 ejabberd의 ejabberd.yml에 정의된 acl 정보이다. 인증을 위한 계정은 ejabberd_auth 모듈을 사용하고 acl에 정의된 계정만 admin 계정으로 사용할 수 있다.

## 참고

  * [cowboy](https://github.com/ninenines/cowboy)