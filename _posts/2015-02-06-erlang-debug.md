---
author: jeidee
categories:
- erlang
date: '2015-02-06'
guid: https://erlnote.wordpress.com/2015/02/06/erlang-debug/
id: 148
permalink: /2015/02/06/erlang-debug/
tags:
- debug
title: erlang debug
url: /2015/02/06/erlang-debug
---

erlang에서 디버깅하는 방법은 다음과 같다.

  * 주의할 것은 erlang 설치 전에 wxWiget 라이브러리를 먼저 설치한 후 erlang을 설치해야 한다는 것이다.
  * 관련 설치방법은 erlang/otp 설치 문서를 참고!

erl 노드를 실행시킨 후 debugger monitor를 실행시킨다.

```
      
> debugger:start().
  
```

Monitor윈도가 실행되면 Module > Interpret 메뉴를 선택한다.

파일 선택기에서 디버깅하고자 하는 .erl 파일을 선택한다.
  
반드시 erl 노드가 import한 모듈의 소스여야 한다.
  
예를 들어 ejabberd의 디버그 쉘을 실행시킨 후 디버깅할 수 있는 모듈은 ejabberd 모듈에 한정된다.

로드된 소스파일명을 더블클릭해서 소스파일을 열 수 있다.

erl 소스뷰어가 출력되면 원하는 부분을 찾아 마우스로 더블클릭하거나 Break메뉴의 Line Break를 선택해 특정 라인에 브레이크 포인트를 설정할 수 있다.

브레이크 포인트를 설치한 후 프로그램을 실행시키면 Monitor윈도우에 해당 브레이크 포인트가 걸린 프로세스가 출력되며,

해당 프로세스를 더블클릭하면 디버깅을 시작할 수 있다.