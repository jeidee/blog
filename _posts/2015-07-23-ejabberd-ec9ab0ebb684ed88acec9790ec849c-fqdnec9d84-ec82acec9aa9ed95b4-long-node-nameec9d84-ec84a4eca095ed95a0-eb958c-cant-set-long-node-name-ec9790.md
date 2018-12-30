---
author: jeidee
categories:
- ejabberd
date: '2015-07-23'
guid: https://erlnote.wordpress.com/?p=552
id: 552
permalink: /2015/07/23/ejabberd-%ec%9a%b0%eb%b6%84%ed%88%ac%ec%97%90%ec%84%9c-fqdn%ec%9d%84-%ec%82%ac%ec%9a%a9%ed%95%b4-long-node-name%ec%9d%84-%ec%84%a4%ec%a0%95%ed%95%a0-%eb%95%8c-cant-set-long-node-name-%ec%97%90/
tags:
- erlang
title: ejabberd 우분투에서 FQDN을 사용해 long node name을 설정할 때, Can&#8217;t set long node name 에러 발생하는 경우
url: /2015/07/23/ejabberd-ec9ab0ebb684ed88acec9790ec849c-fqdnec9d84-ec82acec9aa9ed95b4-long-node-nameec9d84-ec84a4eca095ed95a0-eb958c-cant-set-long-node-name-ec9790
---

제목과 같이 우분투에서 long node name을 사용해 ejabberd(erlang) 노드를 사용할 경우,
  
ejabberdctl의 status, debug등의 명령을 실행하면 다음과 같은 에러가 발생하는 경우가 있다.

```
  
"Can't set long node name!nPlease check your configurationn",[]}
  
```

예를 들어, 노드명을 ejabberd@hello.world.com 으로 사용할 경우 다음과 같이 hostname을 확인해 본다.

```
  
$ sudo hostname
  
```

hostname이 hello.world.com으로 설정되어 있어야 한다.
  
만약, 제대로 설정되어 있지 않은 경우 다음과 같이 설정한다.

```
  
$ sudo hostname hello.world.com
  
```

영구적으로 사용하고자 한다면 /etc/hostname의 값도 수정해야 한다.

```
  
$ sudo vi /etc/hostname

&#8230;

hello.world.com
  
```

그런 후 /var/lib/ejabberd/ 하위의 파일을 모두 삭제한 후 다시 ejaberd 노드를 시작해 주면 된다.

```
  
$ rm -rf /var/lib/ejabberd/*
  
$ ejabberdctl start
  
```