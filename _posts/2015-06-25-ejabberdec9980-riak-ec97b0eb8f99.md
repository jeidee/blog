---
id: 537
title: ejabberd와 riak 연동
date: 2015-06-25T10:06:08+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=537
permalink: '/2015/06/25/ejabberd%ec%99%80-riak-%ec%97%b0%eb%8f%99/'
categories:
  - ejabberd
  - riak
tags:
  - nosql
---
## ejabberd 설치

ejabberd에 riak를 활성화해서 설치하는 방법은 [이전 포스트](https://erlnote.wordpress.com/2015/01/31/ejabberd-riak-%ED%99%9C%EC%84%B1%ED%99%94-%EC%84%A4%EC%B9%98/)를 참고해서 설치한 후, [공식 문서](http://docs.ejabberd.im/admin/guide/configuration/#database-and-ldap-configuration)를 참고해서 설정한다.

## riak 설치

riak 설치방법은 여러 방법이 있지만 ubuntu에서 간단히 다음 방법을 사용해 설치한다.

```
  
wget http://s3.amazonaws.com/downloads.basho.com/riak/2.1/2.1.1/ubuntu/precise/riak\_2.1.1-1\_amd64.deb
  
sudo dpkg -i riak\_2.1.1-1\_amd64.deb
  
```

### riak 주요 파일 위치

위와 같이 설치되면 다음의 디렉토리에 주요 파일들이 위치하게 된다.

1) 실행파일
  
/usr/sbin/riak
  
/usr/sbin/riak-admin
  
/usr/sbin/riak-debug

2) 설정파일
  
/etc/riak/riak.conf

3) 로그파일
  
/var/log/riak/console.log
  
/var/log/riak/crash.log
  
/var/log/riak/error.log
  
/var/log/riak/erlang.log
  
/var/log/riak/run_erl.log

### riak 실행 확인

설치가 완료되면 다음과 같이 riak를 시작한다.

```
  
$ sudo riak start
  
```

정상적으로 실행되었고 에러가 없는지 확인한다.

```
  
$ sudo riak ping
  
pong
  
```

pong이 출력되고 crash.log와 error.log에 로그가 없다면 정상 실행된 것이다.

```
  
$ sudo vi /var/log/riak/crash.log
  
$ sudo vi /var/log/riak/error.log
  
```

## riak ejabberd 연동 설정

riak를 설치한 후 [ejabberd의 riak 연동 문서](http://docs.ejabberd.im/admin/guide/configuration/#database-and-ldap-configuration)를 보고 다음과 같이 riak의 환경설정을 수정한다.

### ejabberd.yml 수정

```
  
default_db: riak
  
```

이 외에도 다음과 같이 riak 연결 설정을 할 수 있다.

  * riak_server: String # 기본값은 localhost이므로 생략 가능
  * riak_port: Port # 기본값은 8087이며 생략 가능
  * riak\_pool\_size: N # 기본값은 10이며 생략 가능. ejabberd가 열게 되는 riak 연결 수
  * riak\_start\_interval: N # 기본값은 30초. Riak 연결이 실패하면 재시도 대기하는 시간

### riak.conf 수정

```
  
$ sudo vi /etc/riak/riak.conf

&#8230;
  
storage_backend = leveldb

```

### vm.args 생성

```
  
$ sudo vi /etc/riak/vm.args

&#8230;
  
-pz /lib/ejabberd/ebin
  
```

ejabberd/ebin 디렉토리는 ejabberd를 어디에 설치했는지에 따라 다르므로 각자의 환경에 맞게 수정한다.

### riak 재실행

```
  
$ sudo riak restart
  
```

위와 같이 재실행하고 나면 riak가 정상실행되지 않고 crash된다.
  
console.log를 확인하면 다음과 같은 에러메세지와 함께 crash 로그를 확인할 수 있다.

```
  
[error] <0.186.0> gen\_server riak\_core\_capability terminated with reason: no function cla use matching orddict:fetch(nonode@nohost, [{'riak@127.0.0.1',[{ {riak\_control,member\_info\_version},[v1,v0]},{ {riak \_core,bucket\_types},[true,&#8230;]},&#8230;]}]) line 72

```

우리가 만든 vm.args에 -pz 이외의 필요한 설정값들이 없기 때문이다.
  
위의 에러는 노드명이 지정되지 않아서 생긴 문제인데, vm.args파일을 다른 이름으로 변경한 후 다시 riak를 실행해 보자.

```
  
$ sudo mv /etc/riak/vm.args /etc/riak/vm.args.bak
  
$ sudo riak start
  
```

그런 후 riak 프로세스를 살펴보면 다음과 같이 erlang vm의 시작 옵션에 -vm_args 옵션이 설정되어 있음을 확인할 수 있다.

```
  
$ ps -ef | grep riak

&#8230;
  
riak 9534 9531 22 09:48 pts/12 00:00:02 /usr/lib/riak/erts-5.10.3/bin/beam.smp -scl false -sfwi 500 -P 256000 -e 256000 -Q 65536 -A 64 -K true -W w -zdbbl 32768 &#8212; -root /usr/lib/riak -progname riak &#8212; -home /var/lib/riak &#8212; -boot /usr/lib/riak/releases/2.1.0/riak -config /var/lib/riak/generated.configs/app.2015.06.25.09.48.44.config -setcookie riak -name riak@127.0.0.1 -smp enable -vm_args /var/lib/riak/generated.configs/vm.2015.06.25.09.48.44.args -pa /usr/lib/riak/lib/basho-patches &#8212; console
  
```

/var/lib/riak에는 riak에서 생성한 데이터파일과 기타 설정 파일들이 위치하는데, generated.configs 디렉토리에 vm.2015.06.25.09.48.44.args 파일이 있음을 확인할 수 있다.

해당 파일을 열어보면 다음과 같은 내용을 확인할 수 있다.

```
  
+scl false
  
+sfwi 500
  
+P 256000
  
+e 256000
  
-env ERL\_CRASH\_DUMP /var/log/riak/erl_crash.dump
  
-env ERL\_FULLSWEEP\_AFTER 0
  
+Q 65536
  
+A 64
  
-setcookie riak
  
-name riak@127.0.0.1
  
+K true
  
+W w
  
-smp enable
  
+zdbbl 32768
  
```

위 값들은 erlang vm의 시작옵션들이며 각각의 옵션이 의미하는 바는 [Erl](http://erlang.org/doc/man/erl.html)문서를 참고하도록 한다.

위의 옵션들을 우리가 만든 vm.args.bak파일에 추가해 준다.(vm.args.bak에는 -pz 옵션만 설정되어 있다.)
  
vm.args.bak파일을 vm.args로 변경한 후 riak를 재실행해 준다.

riak를 재실행한 후 다음과 같은 에러가 console.log에 출력된다면 다음과 같이 cookie를 수정해 주도록 한다.

```
  
[error] <0.2424.0> \*\* Connection attempt from disallowed node 'riak\_maint\_10788@127.0.0.1' \*\*
  
```

ejabberd 노드의 cookie를 다음과 같이 확인한 후,

```
  
$ ejabberdctl debug

> erlang:get_cookie().
  
'XGKYILEFXQKKCTWXBVFA'
  
```

vm.args의 -setcookie 옵션을 다음과 같이 변경해 준다.

```
  
-setcookie XGKYILEFXQKKCTWXBVFA
  
```

그런 후 /var/lib/riak안에 생성된 파일들을 모두 제거한 후 다시 riak를 실행하면 된다.

## 참고

  * [ejabberd riak 활성화 설치](https://erlnote.wordpress.com/2015/01/31/ejabberd-riak-%ED%99%9C%EC%84%B1%ED%99%94-%EC%84%A4%EC%B9%98/)

  * [ejabberd configuration guide](http://docs.ejabberd.im/admin/guide/configuration)

  * [riak installing guide](http://docs.basho.com/riak/latest/ops/building/installing/debian-ubuntu/)