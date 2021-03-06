---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-clustering/
id: 37
permalink: /2015/01/21/ejabberd-clustering/
tags:
- clustering
title: ejabberd clustering
url: /2015/01/21/ejabberd-clustering
---

process-one 사이트에 명시된 p1-spec 문서는 찾을 수가 없었다.
  
대신 install guide에 포함된 clustering guide를 살펴보고 cluster-node를 구축하는 방법을 살펴보도록 한다.

### cluster 구성을 위한 환경

다음과 같이 Virtualbox를 사용해 Windows 7 호스트에 ubuntu 14.10 게스트 OS를 설치해서 클러스터링을 구성한다.

  * 호스트 OS 
      * Windows 7
  * 게스트 OS #1 
      * Ubuntu 14.10 desktop
      * ip: 192.168.56.101
      * hostname: hello.world
      * ejabberd 역할: slave node
  * 게스트 OS #2 
      * Ubuntu 14.10 desktop
      * ip: 192.168.56.102
      * hostname: foo.bar
      * ejabberd 역할: master node

우선 게스트 OS는 하나만 설치하는 데, 추후 복제를 통해 게스트 OS를 하나 더 생성해야 한다.
  
이렇게 구성한 후 게스트 OS간 통신을 위해 네트워크 설정을 변경해야 하는데, 다음 링크를 참조한다.
  
http://erlnote.tumblr.com/post/107265239154/virtualbox

### hostname 변경

long name host를 테스트하기 위해 다음과 같이 hostname을 임의로 변경해 준다.

#### /etc/hosts 수정

```
      
127.0.0.1 localhost
      
127.0.1.1 vagrant-VirtualBox
      
192.168.56.101 hello.world
  
```

#### hostname 설정

```
      
$ hostname hello.world
  
```

위와 같이 현재 서버의 호스트명을 변경 후 재 로그인한다.

또는 /etc/hostname을 변경해도 되며, 테스트를 위해 nameserver는 127.0.0.1이 되어야 한다.

\*\* /etc/hostname \*\*

```
        
1 hello.world
  
```

\*\* /etc/resolv.conf \*\*

```
        
\# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
        
\# DO NOT EDIT THIS FILE BY HAND &#8212; YOUR CHANGES WILL BE OVERWRITTEN
        
nameserver 127.0.1.1
  
```

### ejabberd 설치

ejabberd를 설치한 후 다음과 같이 설정파일을 변경하도록 한다.

#### ejabberdctl.cfg 수정

기본 ejabberd erlang 노드는 ejabberd@localhost이기 때문에 ejabberdctl.cnf를 다음과 같이 수정해서 노드명을 수정해 준다.

```
      
\# Default: ejabberd@localhost
      
#
      
ERLANG_NODE=ejabberd@hello.world
  
```

#### ejabberd.yml 수정

**hosts 수정**

```
       
hosts:
         
&#8211; "hello.world"
         
&#8211; "localhost"
  
```

**host_config 수정**
  
만약 다른 가상호스트와 다른 인증을 원한다면 다음과 같이 설정을 수정한다.

```
      
host_config:
        
"hello.world":
           
auth_method:
             
&#8211; internal
  
```

**acl 수정**
  
localhost 가상호스트의 admin 계정만 관리권한을 부여하기 위해 다음과 같이 수정한다.

```
      
acl:
        
##
        
\## The 'admin' ACL grants administrative privileges to XMPP accounts.
        
\## You can put here as many accounts as you want.
        
##
        
admin:
          
user:
            
&#8211; "admin": "localhost"
  
```

### ejabberd 테스트

localhost에 admin 계정을 추가한다.

```
      
$ ejabberdctl start
      
$ ejabberdctl register admin localhost 1234
  
```

만약 다음과 같은 에러가 발생할 경우 hostname이 제대로 설정되지 않아서이므로 이 부분을 확인한 후 다시 테스트하도록 한다.

```
      
"Can't set long node name!nPlease check your configurationn"
  
```

### 게스트 OS 복제

위와 같이 101서버를 하나 정상적으로 설정했다면 virtualbox에서 복제 기능을 사용해 게스트OS를 복제하도록 하자.

복제된 새로운 게스트 OS의 IP는 192.168.56.102이며 hostname은 foo.bar로 수정한다.

### easy_cluster 설정

[easy_cluster의 basic 설정](http://chad.ill.ac/post/35967173942/easy-ejabberd-clustering-guide-mnesia-mysql) 문서를 참고해서 다음과 같이 설정한다.

#### foo.bar

**ejabberdctrl.cfg**

```
       
#
       
INET\_DIST\_INTERFACE=192.168.56.102
  
```

**/ets/hosts**

```
      
192.168.56.101 hello.world
      
192.168.56.102 foo.bar
  
```

#### hello.world

**ejabberdctrl.cfg**

```
       
#
       
INET\_DIST\_INTERFACE=192.168.56.101
  
```

**/ets/hosts**

```
      
192.168.56.101 hello.world
      
192.168.56.102 foo.bar
  
```

**easy_cluster.erl**

```
      
$ git clone https://github.com/chadillac/ejabberd-easy_cluster.git
      
$ cd ejabberd-easy_cluster
      
$ cp easy_cluster.erl ~/ejabberd/src/
      
$ cd ~/ejabberd
      
$ make & make install
  
```

~/ejabberd 경로는 ejabberd의 소스 경로이다.

위와 같이 easy_cluster.erl을 배포한 후에 ejabberdctl start를 한 후 다음과 같이 debug 쉘을 실행한다.

```
      
$ ejabberdctl debug
      
(ejabberd@hello.world)3> easy\_cluster:test\_node('ejabberd@foo.bar').
      
server is reachable.
      
ok
  
```

테스트가 성공하지 않는 경우 foo.bar 서버와 hello.world 서버의 다음 항목을 살펴본다.

  * erl cookie
  * 4369 tcp port가 오픈 되어 있는지 확인한다.

그래도 안 될 경우 다음 문서를 참고한다.
  
http://www.erlang.org/doc/man/epmd.html

마지막으로 다음과 같이 foo.bar 노드에 join하도록 한다.

```
      
(ejabberd@hello.world)4> easy_cluster:join('ejabberd@foo.bar').
      
ok
  
```

ok가 떨어지면 hello.world 노드가 foo.bar 노드에 연결된 것이다.

### easy_cluster:join()의 동작 원리

foo.bar는 master node이고 hello.world는 slave node이다.
  
master node의 역할은 ejabberd의 mnesia db를 보유한 node이며, slave node는 master node의 mnesia db를 공유하게 된다.

즉, hello.world(salve) node에서 join(&#8216;ejabberd@foo.bar&#8217;)를 하는 순간, 두 노드는 클러스터로 묶이고 DB는 foo.bar(master) node의 것을 사용하게 된다.

join/1 함수를 보면 다음과 같다.

```erlang
      
join(NodeName) ->
              
mnesia:stop(),
              
mnesia:delete_schema([node()]),
              
mnesia:start(),
              
mnesia:change\_config(extra\_db_nodes, [NodeName]),
              
mnesia:change\_table\_copy\_type(schema, node(), disc\_copies),
              
application:stop(ejabberd),
              
application:start(ejabberd).
  
```

현재 노드(hello.world slave node)의 mnesia 스키마를 비우고 change_config/2 함수를 사용해 master node의 mnesia에 클러스터로 연결한다.
  
mnesia:change\_config(extra\_db\_nodes,&#8230;) 함수를 사용해 클러스터에 조인하려면 현재 노드의 스키마를 비운상태(delete\_schema/1)에서 조인해야 한다.
  
(스키마를 삭제하고 새로 mnesia:start/0를 할 경우 저장 타입은 ram type이 된다.)

master node의 mnesia db에 연결한 후 현재 노드의 스키마 저장 타입을 disc_copies로 변경해 노드가 종료되어도 스키마가 삭제되지 않도록 한다.

그리고 ejabberd 어플리케이션을 재시작하면 ejabberd 클러스터 가입이 완료된다.

### slave 노드를 master 노드로 변경

만약 hello.world(slave) 노드를 master 노드로 하고 싶은 경우 먼저 master 노드의 mnesia 데이터베이스를 동기화 한 후 join을 완료해야 한다.

```erlang
      
join\_as\_master(NodeName) ->
              
application:stop(ejabberd),
              
mnesia:stop(),
              
mnesia:delete_schema([node()]),
              
mnesia:start(),
              
mnesia:change\_config(extra\_db_nodes, [NodeName]),
              
mnesia:change\_table\_copy\_type(schema, node(), disc\_copies),
              
easy\_cluster:sync\_node(NodeName),
              
application:start(ejabberd).

sync_node(NodeName) ->
              
[{Tb, mnesia:add\_table\_copy(Tb, node(), Type)}
              
|| {Tb, [{NodeName, Type}]} <- [{T, mnesia:table\_info(T, where\_to_commit)}
              
|| T <- mnesia:system_info(tables)]].
  
```

### 개선점

다음과 같은 항목을 개선해야 한다.

  * sync 자동화
  * 새로운 노드 추가 자동화
  * fail-over 자동화

### References

  * http://www.process-one.net/docs/ejabberd/guide_en.html#htoc89
  * http://chad.ill.ac/post/35967173942/easy-ejabberd-clustering-guide-mnesia-mysql
  * https://github.com/chadillac/ejabberd-easy_cluster/