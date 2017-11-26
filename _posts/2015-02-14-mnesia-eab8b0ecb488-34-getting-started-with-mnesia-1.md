---
id: 172
title: 'mnesia 기초 3/4 &#8211; Getting Started with Mnesia 1/2'
date: 2015-02-14T21:41:38+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=172
permalink: '/2015/02/14/mnesia-%ea%b8%b0%ec%b4%88-34-getting-started-with-mnesia-1/'
categories:
  - erlang
tags:
  - mnesia
---
Erlang 공식 문서 중에 [Getting Started with Mnesia](http://www.erlang.org/doc/apps/mnesia/Mnesia_chap2.html)문서를 기준으로 몇몇내용을 추가해 다음과 같이 작성해 보았다.

## mnesia 시작

mnesia를 시작하기 위해서 다음의 과정이 필요하다.

  * erlang node에 mnesia의 데이터가 저장될 경로를 지정하여 erlang shell을 시작한다.
  * 새로운 빈 스키마를 생성한다. 스키마는 데이터베이스의 정보를 포함한다.
  * mnesia DBMS를 시작한다.
  * 테이블을 생성한다.
  * 데이터베이스 정보를 조회한다.

위의 과정을 수행하기 위해 다음과 같이 따라해 보자.

```
      
$ erl -mnesia dir '"tmp/funky"'
  
```

tmp/funky 디렉토리에 mnesia 데이터가 저장되도록 -mnesia dir 옵션을 사용해 erlang shell을 시작한다.

그 이후 위에서 언급한 순서대로 다음과 같이 입력한다

```erlang
      
Eshell V6.3 (abort with ^G)
      
1> mnesia:create_schema([node()]).
      
ok
      
2> mnesia:start().
      
ok
      
3> mnesia:create_table(funky, []).
      
{atomic,ok}
      
4> mnesia:info().
      
&#8212;> Processes holding locks <&#8212;
      
&#8212;> Processes waiting for locks <&#8212;
      
&#8212;> Participant transactions <&#8212;
      
&#8212;> Coordinator transactions <&#8212;
      
&#8212;> Uncertain transactions <&#8212;
      
&#8212;> Active tables <&#8212;
      
funky : with 0 records occupying 305 words of mem
      
schema : with 2 records occupying 529 words of mem
      
===> System info in version "4.12.4", debug level = none <===
      
opt_disc. Directory "/Users/shinchangheon/tmp/funky" is used.
      
use fallback at restart = false
      
running db nodes = [nonode@nohost]
      
stopped db nodes = []
      
master node tables = []
      
remote = []
      
ram_copies = [funky]
      
disc_copies = [schema]
      
disc\_only\_copies = []
      
[{nonode@nohost,disc_copies}] = [schema]
      
[{nonode@nohost,ram_copies}] = [funky]
      
3 transactions committed, 0 aborted, 0 restarted, 2 logged to disc
      
0 held locks, 0 in queue; 0 local transactions, 0 remote
      
0 transactions waits for other nodes: []
      
ok
  
```

빈 옵션'[]&#8217;으로 테이블을 생성하면 메모리에 테이블이 생성되며, 노드가 종료되면 해당 테이블은 사라진다.

```erlang
      
Eshell V6.3 (abort with ^G)
      
1> mnesia:info().
      
===> System info in version {mnesia\_not\_loaded,nonode@nohost,
                                      
{1423,913120,829264}}, debug level = none <===
      
opt_disc. Directory "/Users/shinchangheon/tmp/funky" is used.
      
use fallback at restart = false
      
running db nodes = []
      
stopped db nodes = [nonode@nohost]
      
ok
  
```

## 테이블을 생성하기

다음과 같은 ERD를 구성한다고 가정해 보자.

![ER-D](http://www.erlang.org/doc/apps/mnesia/company.gif)

Company 데이터베이스에 Dept, Employee, Project 테이블이 있고, Dept와 Employee간에 Manager와 At\_dep 관계가 있으며, Employee와 Project간에 In\_proj 관계가 있는 구성이다.

우선 각 테이블의 스키마를 구성하기 위해 record를 정의한다. 보통 record정의는 .hrl 파일에 모아두며 .erl 모듈에서 include하여 사용한다.

다음은 company.hrl 파일의 내용이다.

```erlang
      
-record(employee, {emp_no,
                         
name,
                         
salary,
                         
sex,
                         
phone,
                         
room_no}).

-record(dept, {id,
                     
name}).

-record(project, {name,
                        
number}).

-record(manager, {emp,
                        
dept}).

-record(at_dep, {emp,
                       
dept_id}).

-record(in_proj, {emp,
                        
proj_name}).
  
```

보통 record의 이름과 table 이름은 동일하게 만든다.
  
company.erl 파일은 다음과 같다.

```erlang
      
-module(company).

%% API
      
-export([init/0, start/0]).

-include_lib("stdlib/include/qlc.hrl").
      
-include("company.hrl").

start() ->
        
mnesia:create_schema([node()]),
        
mnesia:start().

init() ->
        
mnesia:create_table(employee,
          
[{attributes, record_info(fields, employee)}]),
        
mnesia:create_table(dept,
          
[{attributes, record_info(fields, dept)}]),
        
mnesia:create_table(project,
          
[{attributes, record_info(fields, project)}]),
        
mnesia:create_table(manager, [{type, bag},
          
{attributes, record_info(fields, manager)}]),
        
mnesia:create\_table(at\_dep,
          
[{attributes, record\_info(fields, at\_dep)}]),
        
mnesia:create\_table(in\_proj, [{type, bag},
          
{attributes, record\_info(fields, in\_proj)}]).
  
```