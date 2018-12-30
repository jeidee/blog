---
author: jeidee
categories:
- couchbase
- erlang
date: '2017-01-25'
guid: https://erlnote.wordpress.com/?p=1780
id: 1780
permalink: /2017/01/25/couchbase-cberl%ec%97%90-lcb_cntl-%ed%95%a8%ec%88%98-%ec%b6%94%ea%b0%80/
tags:
- cberl
title: couchbase cberl에 lcb_cntl 함수 추가
url: /2017/01/25/couchbase-cberlec9790-lcb_cntl-ed95a8ec8898-ecb694eab080
---

cberl에 libcouchbase의 옵션을 설정하거나 조회할 수 있는 lcb_cntl 함수를 추가해 보자.

새로운 함수 추가를 위해 코맨드 상수를 추가해 준다.
  
cberl_nif.h를 열어 다음행을 추가해 준다.

```cpp
  
#define CMD_CNTL 8
  
```

새로운 코맨드에 콜백함수를 등록해 주기 위해서 cberl\_nif.c소스의 NIF(cberl\_nif_new) 함수에 다음행을 추가해 준다.

https://gist.github.com/jeidee/86967c348f64cee9b37c6bf41c207606

cntl 함수의 매개변수를 위한 구조체와 함수선언문을 cb.h에 다음과 같이 추가한다.

https://gist.github.com/jeidee/430f7cea10bc95099cafb79e71407f42

cntl 함수 정의부를 cb.c에 다음과 같이 작성한다.

https://gist.github.com/jeidee/07acab77aad96f32b4f79110ae202209

이제 erlang 소스를 수정해 보자.
  
cntl 함수가 erlang에서 호출될 수 있도록 cberl.erl 소스파일에 다음과 같이 API를 작성한다.

https://gist.github.com/jeidee/38adf8dc3e63723a9843ec3b3826cd76

Mode값이 0일 경우 조회, 1일 경우 새로운 값으로 설정한다.
  
Cmd는 libcouchbase.h의 cntl.h파일에 정의되어 있다.

실제 cberl의 erlang worker process에서 cntl 메세지를 받아 처리하기 위해 다음과 같이 cberl_worker.erl 소스파일에 추가한다.

https://gist.github.com/jeidee/18189bb8c7a91ffc356620f87910db63

여기 까지 포함된 소스는 github 리포지토리에 addcntl 브랜치로 작성해 놓았다.

erlang rebar 프로젝트에서 참조해 사용하기 위해서는 다음과 같이 rebar.config의 deps 섹션에 추가해야 한다.

```
          
{cberl, ".*", {git, "https://github.com/jeidee/cberl.git", {branch, "addcntl"}}},
  
```