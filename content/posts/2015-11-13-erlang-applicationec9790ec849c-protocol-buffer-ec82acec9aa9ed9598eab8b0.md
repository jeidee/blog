---
author: jeidee
categories:
- erlang
date: '2015-11-13'
guid: https://erlnote.wordpress.com/?p=616
id: 616
permalink: /2015/11/13/erlang-application%ec%97%90%ec%84%9c-protocol-buffer-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/
tags:
- google protocol buffer
- gpb
- proto
title: erlang application에서 protocol buffer 사용하기
url: /2015/11/13/erlang-applicationec9790ec849c-protocol-buffer-ec82acec9aa9ed9598eab8b0
---

이번 역시, [rebar를 사용해 프로젝트를 생성](https://erlnote.wordpress.com/2015/11/06/erlang-rebar%EC%9D%98-project-template%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-application-%EB%BC%88%EB%8C%80-%EB%A7%8C%EB%93%A4%EA%B8%B0/)했다고 가정한다.

basho의 protobuffs 라이브러리를 추가해 보도록 하자.

## rebar.config 수정

[이전 포스트](https://erlnote.wordpress.com/2015/11/11/erlang-application%EC%97%90-lager%EB%A1%9C%EA%B1%B0-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0/)를 참고해서 기본 작업(sub_dirs 추가 등)은 완료했다고 가정한다.

```
  
{deps, [
  
{'lager', ".*", {git, "https://github.com/basho/lager", ""}},
  
{protobuffs, ".*", {git, "git://github.com/basho/erlang_protobuffs.git", {branch, "master"}}}
  
]}.
  
```

  * deps에 protobuffs, &#8230; 행을 복사해 넣는다.

## xxx.proto 파일 생성

프로토콜을 정의한 *.proto파일을 생성한다.

예를 들면 다음과 같은 simple.proto파일을 src 디렉토리 밑에 추가한다.

```
  
package simple;

message Person {
  
required string name = 1;
  
required string address = 2;
  
required string phone_number = 3;
  
required int32 age = 4;
  
optional Location location = 5;
  
}

message Location
  
{
  
required string region = 1;
  
required string country = 2;
  
}
  
```

make로 컴파일하고 나면 rebar가 .proto파일을 자동으로 인식해 include 디렉토리(src와 동일 레벨)를 생성하고, simple_pb.hrl파일을 생성한다.
  
simple\_pb.erl파일은 컴파일 완료 후 ebin 디렉토리에 simple\_pb.beam 파일을 생성 후 자동으로 삭제된다.

## 테스트

make console 명령으로 console에서 어플리케이션을 시작한다.
  
erl쉘이 출력되면 다음과 같이 encoding, decoding을 할 수 있다.

  * simple_pb.hrl에는 record가 정의되어 있지만 쉘에서는 rd()명령어로 레코드를 정의후 테스트할 수 있다.

```
  
> rd(location, {region, country}).
  
> L1 = simple\_pb:encode\_location(#region{region="Korea", country="Seoul"}).
  
> L2 = list\_to\_binary(L1).
  
> simple\_pb:decode\_location(L2).
  
```

## 참고

좀 더 자세한 내용은 다음 링크를 참고한다.

  * [erlang_protobuffs](https://github.com/basho/erlang_protobuffs)