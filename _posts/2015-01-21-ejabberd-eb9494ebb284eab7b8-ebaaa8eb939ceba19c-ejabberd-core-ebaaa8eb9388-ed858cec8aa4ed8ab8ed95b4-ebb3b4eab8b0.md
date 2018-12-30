---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-%eb%94%94%eb%b2%84%ea%b7%b8-%eb%aa%a8%eb%93%9c%eb%a1%9c-ejabberd-core-%eb%aa%a8%eb%93%88-%ed%85%8c%ec%8a%a4%ed%8a%b8%ed%95%b4-%eb%b3%b4%ea%b8%b0/
id: 27
permalink: /2015/01/21/ejabberd-%eb%94%94%eb%b2%84%ea%b7%b8-%eb%aa%a8%eb%93%9c%eb%a1%9c-ejabberd-core-%eb%aa%a8%eb%93%88-%ed%85%8c%ec%8a%a4%ed%8a%b8%ed%95%b4-%eb%b3%b4%ea%b8%b0/
tags:
- debug
title: ejabberd 디버그 모드로 ejabberd  core 모듈 테스트해 보기
url: /2015/01/21/ejabberd-eb9494ebb284eab7b8-ebaaa8eb939ceba19c-ejabberd-core-ebaaa8eb9388-ed858cec8aa4ed8ab8ed95b4-ebb3b4eab8b0
---

ejabberd를 실행한 상태에서 ejabberdctl의 debug 쉘을 사용해 실행 중인 ejabberd 노드에 접속해 core 모듈을 테스트해 볼 수 있다.

먼저 debug 쉘을 실행한다.

```
  
ejabberdctl debug
  
```

ejabberd 모듈과 api를 확인하려면 ejabberd의 문서를 확인하거나 src 폴더의 .erl 소스파일을 열어 직접 확인해 볼 수 있다.

여러 모듈 중에서 인증 모듈을 사용해 유저가 존재하는지 여부를 체크해 보자.

```erlang
  
(ejabberd@localhost)2> ejabberd\_auth:is\_user_exists(<<"test1">>, <<"admin">>).
  
false
  
(ejabberd@localhost)3> ejabberd\_auth:is\_user_exists(<<"test">>, <<"admin">>).
  
true
  
```

위와 같이 테스트해 볼 수 있고, 실행 중인 ejabberd 노드의 상태를 모니터링하거나 dets/mnesia 테이블의 내용을 확인해 보기 위해 observer를 실행할 수 있다.

```erlang
      
> observer:start().
  
```

erlang을 빌드할 때 wxWidget 라이브러리가 시스템에 설치된 상태라면 observer 윈도우가 문제 없이 실행될 것이다.

> wxWidget 라이브러리를 설치하고 erlang/otp를 인스톨하는 방법은 http://jeidee.tumblr.com/post/105423833606/erlang 문서를 참고하도록 한다.