---
author: jeidee
categories:
- erlang
date: '2016-05-16'
guid: https://erlnote.wordpress.com/?p=1332
id: 1332
permalink: /2016/05/16/erlang-%ed%94%84%eb%a1%9c%ea%b7%b8%eb%9e%98%eb%b0%8d-%ea%b7%9c%ec%b9%99%ea%b3%bc-%ec%bb%a8%eb%b2%a4%ec%85%98/
title: Erlang 프로그래밍 규칙과 컨벤션
url: /2016/05/16/erlang-ed9484eba19ceab7b8eb9e98ebb08d-eab79cecb999eab3bc-ecbba8ebb2a4ec8598
---

## 원문

http://www.erlang.se/doc/programming_rules.shtml

원문을 간략히 요약/정리해 보았습니다.

## SW 공학 원리

  * 하나의 모듈에서는 가능한 적은 수의 함수를 export한다.
  * 모듈간 의존성은 가능한 줄이도록 한다.
  * 공통 코드는 라이브러리로 작성한다.
  * dirty하거나 tricky한 코드는 분리된 모듈로 작성한다.
  
    ** 예를 들면, process의 사전을 사용하거나, 이상한 목적으로 erlang:process_info/1를 사용하거나&#8230;하는 등의
  * 함수호출자가 반환된 결과로 무엇을 할 것인지 가정해서 작성하지 말고, 결과를 돌려주기만 해라.
  * 중복 코드가 발생할 것 같다면 함수를 사용해서 중복 코드를 피하라.
  * Top-down 방식(개괄에서 상세로)으로 코드를 작성해라.
  * 항상 코드를 최적화해서 작성하려고 하지 마라. 필요할 때 필요한 코드만 최적화해라.
  * 일관성을 유지해서 예측 가능한(least astonishment) 코드를 작성해라.
  * side effect를 제거하도록 노력해라.
  * private data의 자료구조가 모듈 밖으로 누출(leak)되지 않도록 해라.
  * 가능한 결정적인 코드(매 실행 결과가 동일)를 작성하도록 한다.
  * 방어적으로 프로그래밍하지 않는다.(validation은 클라이언트 코드에서&#8230;)
  * 장치 드라이버를 위한 하드웨어 인터페이스는 분리한다.
  * 초기화와 해제(예를 들어 파일열고 닫기)를 위한 기능은 같은 함수에서 처리한다.

## 에러 처리

  * 에러 처리 코드와 정상 처리 코드는 분리한다.
  * 커널 에러를 식별한다.

## 프로세스, 서버, 메세지

  * 하나의 모듈에서는 하나의 프로세스만 구현한다.
  * 시스템 구조를 위해 프로세스를 사용할 수 있지만 함수 호출로 대신할 수 있는 경우 프로세스와 메세지 전달은 사용하지 않는다.
  * 이름이 등록된 프로세스를 사용한다.
  * 실세계에서 존재하는 동시성 작업당 하나의 병렬 프로세스를 할당하도록 한다.
  * 프로세스에는 오직 하나의 역할(role)만 할당한다.
  * 가능하다면 서버와 프로토콜 핸들러를 위해서 제네릭 함수를 사용하도록 한다.
  * 메세지는 태그를 붙여 전달한다.(예를 들어 {execute, Mod, Funcs, Args} 등의 형식으로)
  * 명시되지 않은 메세지는 버린다.
  * 서버는 꼬리재귀(tail-recursive)를 사용해 작성한다.
  * 메세지를 전달할 때 직접 전달하지말고, 인터페이스 함수를 사용한다.
  * 타임아웃 : receive 구문에서 after를 사용할 때는 주의해야 한다. 
  * exit 시그널 처리(Trapping exits) : 가능한 적은 프로세스에서 exit 시그널이 처리되어야 한다.

## 다양한 Erlang 컨벤션

  * 자료구조는 레코드를 사용한다.
  * 레코드를 사용할 때는 selector(#person{name = Name})와 constructor(P = #person{name= &#8220;Joe&#8221;})를 사용한다.
  * 반환값은 tag된 값({value, Value})을 사용한다.
  * catch와 throw는 극도로 주의해서 사용해라. 가능하면 사용하지 마라.
  * process dictionary(get/1, put/1)는 극도로 주의해서 사용해라. 가능하면 사용하지 마라. 
  * -import 지시자를 사용하지 않는다.
  * 함수 export는 다음의 경우에만 사용해라
  
    ** 모듈의 사용자 인터페이스
  
    ** 다른 모듈을 위한 인퍼테이스 함수
  
    ** apply, spawn등에서 불리는 함수지만 동일 모듈에서만 사용할 때

## 명시적인 어휘와 스타일 컨벤션

  * 깊은 중첩 코드를 작성하지 않는다.
  * 매우 큰 모듈을 작성하지 않는다.(400줄 이상의 모듈)
  * 매우 큰 함수를 작성하지 않는다.(15줄에서 20줄)
  * 매우 긴 줄의 코드를 작성하지 않는다.(80컬럼 이상)
  * 변수명은 CamelCase를 사용하며 _로 단어를 구분하지 않는다.
  * 함수명은 소문자로만 작성하며 단어사이는 _로 구분한다.
  * 모듈명은 소문자로만 작성하며 단어사이는 _로 구분한다. 모듈 접두어는 동일한 규칙으로 적용한다.
  * 일관성있는 방법으로 코드를 포매팅한다.(들여쓰기, 공백문자 사용등)

## 코드 문서화

  * 코드 속성

```
  
-revision('Revision: 1.14 ').
  
-created('Date: 1995/01/01 11:21:11 ').
  
-created_by('eklas@erlang').
  
-modified('Date: 1995/01/05 13:04:07 ').
  
-modified_by('mbj@erlang').
  
```

  * 코드 명세에 대한 참조를 제공한다.
  * 모든 에러에 대해 문서화한다.
  * 모든 메세지에 대해 문서화한다.
  * 모듈 주석은 %%%, 함수 주석은 %%, 코드 주석은 %
  * 함수 주석

```
  
%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
%% Function: get\_server\_statistics/2
  
%% Purpose: Get various information from a process.
  
%% Args: Option is normal|all.
  
%% Returns: A list of {Key, Value}
  
%% or {error, Reason} (if the process is dead)
  
%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
get\_server\_statistics(Option, Pid) when pid(Pid) ->
    
&#8230;&#8230;
  
```

  * 자료 구조 주석

```
  
%% File: my\_data\_structures.h

%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%% Data Type: person
  
%% where:
  
%% name: A string (default is undefined).
  
%% age: An integer (default is undefined).
  
%% phone: A list of integers (default is []).
  
%% dict: A dictionary containing various information about the person.
  
%% A {Key, Value} list (default is the empty list).
  
%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
-record(person, {name, age, phone = [], dict = []}).
  
```

  * 파일 헤더 &#8211; 카피라이트

```
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%%% Copyright Ericsson Telecom AB 1996
  
%%%
  
%%% All rights reserved. No part of this computer programs(s) may be
  
%%% used, reproduced,stored in any retrieval system, or transmitted,
  
%%% in any form or by any means, electronic, mechanical, photocopying,
  
%%% recording, or otherwise without prior written permission of
  
%%% Ericsson Telecom AB.
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
```

  * 파일 헤더 &#8211; 리비전 히스토리

```
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%%% Revision History
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%%% Rev PA1 Date 960230 Author Fred Bloggs (ETXXXXX)
  
%%% Intitial pre release. Functions for adding and deleting foobars
  
%%% are incomplete
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%%% Rev A Date 960230 Author Johanna Johansson (ETXYYY)
  
%%% Added functions for adding and deleting foobars and changed
  
%%% data structures of foobars to allow for the needs of the Baz
  
%%% signalling system
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
```

  * 파일 헤더 &#8211; 디스크립션

```
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%%% Description module foobar\_data\_manipulation
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%%% Foobars are the basic elements in the Baz signalling system. The
  
%%% functions below are for manipulating that data of foobars and for
  
%%% etc etc etc
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%%% Exports
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
%%% create_foobar(Parent, Type)
  
%%% returns a new foobar object
  
%%% etc etc etc
  
%%%&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
```

  * 오래된 코드에는 주석을 달지 말고 제거해라
  * 소스 코드 버전 시스템을 사용해라(SVN, GIT)

## 가장 일반적인 실수들

  * 긴 함수
  * 깊은 중첩
  * 잘못된 타입을 사용하는 함수
  * 어떤 함수인지 알 수 없는 함수명
  * 어떤 변수인지 알 수 없는 변수명
  * 불필요한 곳에서 사용하는 프로세스
  * 잘못된 자료구조 선택
  * 잘못된 주석, 또는 전혀 주석이 없는 코드
  * 들여쓰기되지 않은 코드
  * put/get을 사용하는 코드
  * 알 수 없는 메세지를 제어하지 않는 코드

## 필수 문서들

  * 모듈과 모듈이 포함하는 모든 export 함수에 대해 다음 설명이 문서화 되어야 한다.
  
    ** 함수 매개변수의 의미와 자료구조 설명
  
    ** 함수 반환 값의 의미와 자료구조 설명
  
    ** 함수의 목적
  
    ** exit/1 함수를 호출할 때, 실패와 종료를 야기한 이유
  * 프로세스간 주고 받는 모든 메세지들에 대한 포맷이 문서화 되어야 한다.
  * 시스템의 모든 등록된 서버의 인터페이스와 그들의 목적이 문서화 되어야 한다.
  * 모든 에러메세지는 문서화 되어야 한다.