---
author: jeidee
categories:
- mongo
date: '2015-12-08'
guid: https://erlnote.wordpress.com/?p=764
id: 764
permalink: /2015/12/08/mongo-sharded-cluster/
tags:
- clustering
- shard
title: mongo sharded cluster
url: /2015/12/08/mongo-sharded-cluster
---

mongodb의 샤드 클러스터를 구성하는 방법은 다음과 같다.

## 준비

mongod가 설치된 서버가 다음과 같이 3대 준비되어 있다고 가정한다.

#1 : 192.168.56.101
  
#1 : 192.168.56.102
  
#1 : 192.168.56.103

## shard 서버 구동

@192.168.56.101/102/103 공통

```
  
nohup mongod &#8211;shardsvr &#8211;dbpath /var/lib/mongo/ > /var/log/mongod.log &
  
```

## config 서버 구동

@192.168.56.101

```
  
mkdir /var/lib/mongo/configdb
  
nohup mongod &#8211;configsvr &#8211;dbpath /var/lib/mongo/configdb > /var/log/mongod_config.log &
  
```

## mongos 구동

@192.168.56.101

```
  
nohup mongos &#8211;configdb 192.168.56.101:27019 &#8211;chunkSize 1 > /var/log/mongos.log &
  
```

## 클러스터 설정

@192.168.56.101

```
  
$ mongo

mongo>sh.addShard('192.168.56.101:30000');
  
mongo>sh.addShard('192.168.56.102:30000');
  
mongo>sh.addShard('192.168.56.103:30000');

mongo>sh.enableShard("test");
  
mongo>sh.shardCollection("test.people", {"_id":"hashed"});
  
mongo>use test
  
mongo>db.people.save({_id:1, age:10});
  
mongo>db.people.save({_id:2, age:10});
  
```

## 확인

@192.168.56.102

```
  
mongo>use test
  
mongo>db.people.find()
  
{ "_id" : 1, "age" : 10 }
  
```

@192.168.56.103

```
  
mongo>use test
  
mongo>db.people.find()
  
{ "_id" : 2, "age" : 10 }
  
```

## 참고

  * [Deploy shard cluster](https://docs.mongodb.org/manual/tutorial/deploy-shard-cluster/)
  * [mongos 통하여 sharding 환경 구성하기](http://mobicon.tistory.com/143)