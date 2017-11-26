---
id: 568
title: erlang 쉘에서 observer를 사용해 원격 노드 접속
date: 2015-08-25T16:41:20+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=568
permalink: '/2015/08/25/erlang-%ec%89%98%ec%97%90%ec%84%9c-observer%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%b4-%ec%9b%90%ea%b2%a9-%eb%85%b8%eb%93%9c-%ec%a0%91%ec%86%8d/'
categories:
  - erlang
tags:
  - observer
---
먼저 A와 B, 두 노드가 있고, A에서 B노드에 연결한다고 가정한다.

두 노드를 연결하기 위해서는 다음 조건을 우선 만족시켜야 한다.

1) short name을 사용하는 경우
  
* A와 B노드는 동일한 네트워크에 있고, 방화벽을 통하지 않는다.
  
* 방화벽을 통해 접속할 경우 B노드의 다음 TCP포트(아래 예에서는 47242)가 Inbound 오픈되어 있어야 한다.

```
  
B> net_adm:names();
  
{ok, [{"ejabberd", 47242}, &#8230;]}
  
```

  * A와 B노드의 erlang secretkey(cookie)는 동일해야 한다.

```
  
$ erl -sname A@node1 -setcookie secret
  
$ erl -sname B@node2 -setcookie secret

A> erlang:get_cookie().
  
secret
  
```

2) long name을 사용하는 경우
  
* A와 B노드에는 FQDN이 설정되어 있고 A노드에서 DNS를 통해 B노드의 다음 포트로 접속이 가능해야 한다.

```
  
B@hello.world.com> net_adm:names();
  
{ok, [{"ejabberd", 47242}, &#8230;]}
  
```

  * A와 B노드의 erlang secretkey(cookie)는 동일해야 한다.

위의 조건을 만족할 경우 다음과 같이 A노드에서 B노드에 연결가능한지 확인한다.

```
  
A> net_adm:ping('B@node2').
  
pong
  
A@hello.world> net_adm:ping('B@foo.bar').
  
pong
  
```

정상적으로 pong이 수신될 경우 다음과 같이 observer를 시작한후 Nodes 메뉴에서 connect node를 하면 된다.

```
  
A> observer:start().
  
```