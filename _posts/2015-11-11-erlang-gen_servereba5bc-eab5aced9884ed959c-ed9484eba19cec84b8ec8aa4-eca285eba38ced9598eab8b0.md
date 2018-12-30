---
author: jeidee
categories:
- erlang
date: '2015-11-11'
guid: https://erlnote.wordpress.com/?p=610
id: 610
permalink: /2015/11/11/erlang-gen_server%eb%a5%bc-%ea%b5%ac%ed%98%84%ed%95%9c-%ed%94%84%eb%a1%9c%ec%84%b8%ec%8a%a4-%ec%a2%85%eb%a3%8c%ed%95%98%ea%b8%b0/
tags:
- gen_server
- stop
title: erlang gen_server를 구현한 프로세스 종료하기
url: /2015/11/11/erlang-gen_servereba5bc-eab5aced9884ed959c-ed9484eba19cec84b8ec8aa4-eca285eba38ced9598eab8b0
---

gen_server를 구현한 프로세스를 종료하려면 callback함수에서 {stop, Reason, NewState}을 반환하면 된다.

일반적으로는 다음과 같은 패턴으로 구현한다.

```erlang
  
handle_cast({stop, Reason}, State) ->
    
{stop, Reason, State};
  
&#8230;

do_stop(Pid) ->
    
gen_server:cast(Pid, {stop, {shutdown, <<"Reason string&#8230;">>}}).
  
```

handle_cast/2에서 {stop, Reason, NewState}가 아니라 {stop, Reason}만 반환할 경우,
  
bad\_return\_value에 따른 프로세스 크래시가 발생하므로 주의한다.

supervisor에 의해 관리되는 프로세스라면 크래시된 프로세스는 정책에 따라 재시작 될 것이다.