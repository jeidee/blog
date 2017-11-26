---
id: 23
title: ejabberd에 json 라이브러리(jiffy) 추가
date: 2015-01-21T23:41:20+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/ejabberd%ec%97%90-json-%eb%9d%bc%ec%9d%b4%eb%b8%8c%eb%9f%ac%eb%a6%acjiffy-%ec%b6%94%ea%b0%80/'
permalink: '/2015/01/21/ejabberd%ec%97%90-json-%eb%9d%bc%ec%9d%b4%eb%b8%8c%eb%9f%ac%eb%a6%acjiffy-%ec%b6%94%ea%b0%80/'
categories:
  - ejabberd
---
ejabberd에서 json 라이브러리를 사용하려면 jiffy를 사용하면 된다.
  
ejabberd가 설치될 때 의존성을 걸어 함께 설치되도록 하려면 다음과 같이 configure에서 &#8211;enable-json을 추가해 주면 된다.

```
      
./configure &#8211;prefix={&#8230;} &#8211;enable-json
  
```

위와 같이 설치하려면 configure를 해 준 후에 rebar를 사용해 의존 라이브러리를 다운로드 받아야 하는데, 다음과 같이 처리한다.

```
      
rebar get-deps
      
rebar compile
  
```

그런 후 make & make install을 실행하자.

만약 jiffy.so 파일의 경로를 찾지 못하는 에러가 발생할 경우 다음과 같이 Makeifle.in 파일을 수정하면 된다.
  
관련 이슈는 다음 링크에서 확인할 수 있다.
  
https://github.com/processone/ejabberd/commit/b550f247e74e86cac20027b8527e3ddd209837bc

```
          
\# Binary system libraries
          
$(INSTALL) -d $(SODIR)
          
$(INSTALL) -m 644 $(DLLs) $(SODIR)
      
+ [ -f $(SODIR)/jiffy.so ] && (cd $(PRIVDIR); ln -sf lib/jiffy.so;)
          
#
          
\# Translated strings
          
$(INSTALL) -d $(MSGSDIR)
  
```

원래는 ln -s lib/jiffy.so 로 되어 있지만, 그럴 경우 설치된 후 재설치하려고 할 경우 이미 존재하는 심볼릭링크 에러를 뱉으며 make가 실패된다.
  
ls -sf &#8230;로 바꾼다.

제대로 설치되었는지 확인하려면 ejabberdctl debug로 디버그 쉘을 생성한 후 다음과 같이 모듈이 정상적으로 올라왔는지 확인한다.

```erlang
      
(ejabberd@localhost)2> m(jiffy).
      
Module jiffy compiled: Date: December 19 2014, Time: 06.33
      
Compiler options: [{outdir,"ebin"},debug_info,{i,"include"}]
      
Object file: /home/chshin/work/server/bin/lib/ejabberd/ebin/jiffy.beam
      
Exports:
               
decode/1
               
decode/2
               
encode/1
               
encode/2
               
module_info/0
               
module_info/1
      
ok
  
```

jiffy만 단독으로 테스트해 보고자 한다면 다음과 같이 erlang shell을 시작해서 테스트해볼 수 있다.
  
(jiffy는 현재 경로 기준으로 ../../deps/jiffy에 있고 컴파일되어 있다고 가정한다.)

```
      
$ erl -pa ../../deps/jiffy/ebin ../../deps/jiffy
      
> application:start(jiffy).
      
> m(jiffy).
      
> jiffy:encode("test").
  
```