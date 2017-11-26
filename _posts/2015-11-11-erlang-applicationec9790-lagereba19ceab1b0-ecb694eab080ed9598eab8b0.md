---
id: 614
title: 'erlang application에 lager(로거)  추가하기'
date: 2015-11-11T18:05:03+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=614
permalink: '/2015/11/11/erlang-application%ec%97%90-lager%eb%a1%9c%ea%b1%b0-%ec%b6%94%ea%b0%80%ed%95%98%ea%b8%b0/'
categories:
  - erlang
tags:
  - lager
  - logger
---
[rebar를 사용해 프로젝트를 생성](https://erlnote.wordpress.com/2015/11/06/erlang-rebar%EC%9D%98-project-template%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-application-%EB%BC%88%EB%8C%80-%EB%A7%8C%EB%93%A4%EA%B8%B0/)했다고 가정한다.

erlang의 로거 라이브러리 중에서 많이 사용되는 basho/lager를 추가해 보도록 하자.

## rebar.config 수정

```
  
%% -\*- erlang -\*-

{sub_dirs, [
    
"apps/chat",
    
"deps",
    
"rel"
  
]}.

{erl_opts, [
    
{parse\_transform, lager\_transform}, %% for lager
    
debug_info,
    
fail\_on\_warning
  
]}.

%% {require\_otp\_vsn, "R14"}.

%% Example, uncomment to retrieve cowboy as a dep.
  
{deps, [
    
{'lager', ".*", {git, "https://github.com/basho/lager", ""}}
  
]}.

```

  * sub_dirs에 deps 디렉토리를 추가하고,
  * erl\_opts(erlang 컴파일러 옵션)에 {parse\_transform, lager_transform}을 추가한다.
  * deps에 {&#8216;lager&#8217;, &#8230;}처럼 lager dependency를 추가한다.

## rel/reltool.config 수정

두 번째로 rel/reltool.config를 다음과 같이 수정한다.

```
  
%% -\*- erlang -\*-

{sys, [
         
{lib_dirs, ["../apps", "../deps"]},
         
{rel, "myweb", "0.1.0",
          
[
           
kernel,
           
stdlib,
           
sasl,
           
lager,
           
chat
          
]},
  
&#8230;
  
```

  * lib_dirs에 ../deps 디렉토리를 추가한다.
  * 모듈에 lager를 추가한다.

## XXX_app.erl 수정

application behaviour를 구현한 모듈의 start/2 함수의 시작부분에 lager:start/0를 추가한다.

```erlang
  
start(\_StartType, \_StartArgs) ->
    
lager:start(),
    
&#8230;
  
```

## 로그 작성

로그를 출력하고자 한다면 다음과 같이 lager의 info, error등의 함수를 사용해 출력한다.

```erlang
  
lager:info("Hello, world!").
  
lager:error("Error raised!").
  
```

## 참고

기타 자세한 내용은 다음 링크를 참고한다.

  * [basho/lager](https://github.com/basho/lager)