---
author: jeidee
categories:
- riak
date: '2015-12-08'
guid: https://erlnote.wordpress.com/?p=724
id: 724
permalink: /2015/12/08/riak-%ed%81%b4%eb%9f%ac%ec%8a%a4%ed%84%b0-%ea%b5%ac%ec%84%b1/
tags:
- clustering
title: riak 클러스터 구성
url: /2015/12/08/riak-ed81b4eb9facec8aa4ed84b0-eab5acec84b1
---

## riak 설치

환경은 centos-6.7, riak 2.1.2를 기준으로 한다.

## riak 설정

riak.conf만 수정해도 된다. (단, 방화벽 설정을 하려면 app.config를 수정해야 한다.)

클러스터로 구성할 2개의 riak 노드가 다음과 같이 준비되어 있다.

  * riak@192.168.56.101
  * riak@192.168.56.102

두 riak 노드간 tcp/udp의 방화벽을 해제하거나 다음 문서를 참고해서 특정 포트를 열어 놓아야 한다.

  * [Riak network security and firewall configurations](https://docs.basho.com/riak/1.1.0/cookbooks/Network-Security-and-Firewall-Configurations/)

### riak.conf 수정

\*\* riak@192.168.56.101 \*\*

```
  
listener.protobuf.internal = 0.0.0.0:8087
  
nodename = riak@192.168.56.101
  
distributed_cookie = riak
  
storage_backend = leveldb
  
```

  * listener.protobuf.internal : riak 포트
  * nodename : riak 노드명
  * distributed_cookie : 클러스터에 참여하는 모든 노드는 동일한 매직쿠키를 설정
  * storage_backend : leveldb로 설정한다. 자세한 내용은 riak문서 참고

우선은 이정도 설정만 하도록 하며, 나머지 설정은 추후 자세히 살펴보도록 한다.

## cluster 구성

다음과 같이 각각의 노드에서 riak를 시작한다.

```
  
$ sudo riak start
  
```

riak@192.168.56.101 노드를 마스터 노드로 가정하고,
  
riak@192.168.56.102 노드에서 다음과 같이 클러스터에 조인한다.

```
  
$ sudo riak-admin cluster join riak@192.168.56.101
  
```

그런 후 현재 클러스트 계획을 확인하고,

```
  
$ sudo riak-admin cluster plan
  
```

문제 없을 경우 최종 커밋한다.

```
  
$ sudo riak-admin cluster commit
  
```

문제없이 커밋이 완료될 경우 다음과 같이 ring members를 확인해 보도록 한다.

```
  
$ sudo riak-admin status | grep ring_members
  
```

최종 확인은 다음과 같이 수행한다.

```
  
$ sudo riak attach
  
(riak@192.168.56.102)1> riak\_core\_ring\_manager:get\_my_ring().
  
```

노드를 추가하려면 위의 순서를 반복하면 된다.

## 관리콘솔

riak.conf의 다음 설정을 off에서 on으로 수정하면 된다.

```
  
riak_control = on
  
```

## 참고

  * [Riak basic cluster setup](https://docs.basho.com/riak/1.2.0/cookbooks/Basic-Cluster-Setup/)
  * [Riak adding and removing nodes](https://docs.basho.com/riak/1.2.0/cookbooks/Adding-and-Removing-Nodes/)
  * [Riak control](http://docs.basho.com/riak/latest/ops/advanced/riak-control/)