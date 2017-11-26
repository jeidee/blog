---
id: 416
title: ejabberd에 redis 라이브러리(eredis) 추가
date: 2015-06-03T18:41:09+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=416
permalink: '/2015/06/03/ejabberd%ec%97%90-redis-%eb%9d%bc%ec%9d%b4%eb%b8%8c%eb%9f%ac%eb%a6%aceredis-%ec%b6%94%ea%b0%80/'
categories:
  - ejabberd
tags:
  - eredis
  - redis
---
ejabberd가 15.03버전이 릴리즈되면서 redis 지원이 추가되었다.
  
클라이언트 세션 관리를 redis로 할 수 있는데 직접 연관된 모듈은 ejabberd_sm 모듈이다.

redis를 사용하려면 다음과 같이 configure에서 &#8211;enable-redis 옵션을 추가해 주면 된다.

```
  
./configure &#8230; &#8211;enable-redis
  
```

위와 같이 해 준후에 rebar를 사용해 의존 라이브러리를 다운로드 받아 컴파일해야 한다.

```
  
rebar get-deps
  
rebar compile
  
```

그런 후 make && make install을 수행하면 된다.

ejabberd.yml에서 sm\_db\_type을 redis로 설정하기 위해서는 다음 라인을 추가해 주면 된다.

```
  
sm\_db\_type: redis
  
```

ejabberd를 실행한 후 클라이언트 접속 후 redis-cli를 사용해 다음과 같이 확인해 볼 수 있다.

```
  
$ ejabberdctl start
  
$ redis-cli
  
> keys *
  
1) "ejabberd:sm:bypass"
  
2) "ejabberd:sm:test1@bypass"
  
> hgetall ejabberd:sm:bypass
  
&#8230;
  
> hgetall ejabberd:sm:test1@bypass
  
&#8230;
  
```

  * ejabberd는 redis에 hash 자료구조를 사용해서 데이터를 사용한다.
  * jid가 test1@bypass인 유저가 접속한 경우
  
    ## 참고

  * [ejabberd Configuration](http://docs.ejabberd.im/admin/guide/configuration/)