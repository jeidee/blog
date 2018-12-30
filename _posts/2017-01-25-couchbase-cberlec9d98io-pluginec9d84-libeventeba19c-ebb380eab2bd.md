---
author: jeidee
categories:
- couchbase
- erlang
date: '2017-01-25'
guid: https://erlnote.wordpress.com/?p=1812
id: 1812
permalink: /2017/01/25/couchbase-cberl%ec%9d%98io-plugin%ec%9d%84-libevent%eb%a1%9c-%eb%b3%80%ea%b2%bd/
tags:
- 45
- cberl
title: couchbase cberl의IO Plugin을 libevent로 변경
url: /2017/01/25/couchbase-cberlec9d98io-pluginec9d84-libeventeba19c-ebb380eab2bd
---

cberl에는 io plugin이 default로 설정되어 있는데 이럴 경우 select를 사용한다. 동시에 열 수 있는 소켓수는 1024로 제한되어 있어, 그 이상을 처리하기 위해서는 libevent로 수정해야 한다.

그렇지 않을 경우 cberl의 view(http)를 사용할 경우 일정한 요청(동시에 열린 소켓수가 1024를 초과하는 시점) 이상일 경우 간헐적으로 unknown error 45(0x2d)를 반환한다.

실제 libcouchbase의 로그를 cntl함수를 사용해 erlang application process에 attach된 상태에서 stderr에 출력되는 로그를 확인해 보면 다음과 같은 오류가 출력되는 것을 확인할 수 있다.
  
(cberl:cntl 함수를 사용하려면 이전 포스트를 참고해서 cberl 소스를 수정해야 한다.)

```
  
%% LCB\_CNTL\_CONLOGGER_LEVEL
  
cberl:cntl(?POOL_ID, 1, 16#29, 5). 

COUCHBASE: too many FDs. Cannot have socket FD_SETSIZE. Use other I/O plugin
  
```

https://developer.couchbase.com/documentation/server/current/sdk/c/start-using-sdk.html

해당 문서에는 다음과 같이 적시되어 있다.

> You should install the libcouchbase2-libevent plugin in case your application will use more than 1024 file descriptors. The default select() based event loop only supports 1024 file descriptors. 

libevent로 변경하기 위해서는 cberl의 cb.c 소스를 다음과 같이 수정해야 한다.

https://gist.github.com/jeidee/62ed9a02b844c4e6efc587ff5ca5d973

그리고 실행될 erlang application이 있는 서버에 libcouchbase2-libevent 플러그인이 설치되어 있어야 한다.

centos 6에서는 다음과 같이 설치할 수 있다.

```
  
\# wget http://packages.couchbase.com/releases/couchbase-release/couchbase-release-1.0-0-x86_64.rpm
  
\# rpm -i couchbase-release-1.0-0-x86_64.rpm
  
\# yum install libcouchbase2-libevent
  
```