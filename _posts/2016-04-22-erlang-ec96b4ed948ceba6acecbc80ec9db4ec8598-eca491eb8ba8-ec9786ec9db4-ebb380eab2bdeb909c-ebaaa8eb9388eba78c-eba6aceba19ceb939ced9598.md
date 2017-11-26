---
id: 1089
title: erlang 어플리케이션 중단 없이 변경된 모듈만 리로드하기
date: 2016-04-22T14:45:49+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1089
permalink: '/2016/04/22/erlang-%ec%96%b4%ed%94%8c%eb%a6%ac%ec%bc%80%ec%9d%b4%ec%85%98-%ec%a4%91%eb%8b%a8-%ec%97%86%ec%9d%b4-%eb%b3%80%ea%b2%bd%eb%90%9c-%eb%aa%a8%eb%93%88%eb%a7%8c-%eb%a6%ac%eb%a1%9c%eb%93%9c%ed%95%98/'
categories:
  - ejabberd
---
erlang 어플리케이션이 작동 중인 상황에서 중단 없이 변경된 모듈만 리로드 하고 싶은 경우가 있다.
  
단순히 .beam 파일만 교체하는 것으로 해결되지는 않으며,
  
변경된 .beam 파일로 교체(copy)한 후 실행 중인 어플리케이션에 콘솔로 접속한 후 다음과 같이 변경된 모듈을 명시적으로 리로드해야 한다.

```erlang
  
> code:purge(M), code:load_file(M).
  
```

code:purge/1 함수는 정해진 모듈을 메모리에서 제거하는데, 해당 모듈을 사용하는 프로세스가 있을 경우 먼저 해당 프로세스들을 kill하게 된다.
  
프로세스를 kill할 수 없는 경우 code:purge/1는 false를 리턴한다.

보통 서버 모듈(gen_server를 구현했거나 자체 루프를 가지고 프로세스 형태로 존재하는)의 경우 code:purge/1함수를 호출하면 false를 반환할 것이다.

즉, 현재 동작 중인 서버 프로세스는 교체할 수 없다. OTP 프레임웍을 구현한 서버 모듈의 경우 code_change/3 behaviour를 구현하는 것으로 목적을 달성할 수 있다.

다음 코드는 .beam 파일의 버전을 확인해서 변경된 모듈을 리스트로 구하는 코드이다.

```erlang
  
all_changed() ->
      
[M || {M, Fn} <- code:all\_loaded(), is\_list(Fn), is_changed(M)].

is_changed(M) ->
      
try
          
module\_vsn(M:module\_info()) =/= module\_vsn(code:get\_object_code(M))
      
catch \_:\_ ->
          
false
      
end.

module\_vsn({M, Beam, \_Fn}) ->
      
{ok, {M, Vsn}} = beam_lib:version(Beam),
      
Vsn;
  
module\_vsn(L) when is\_list(L) ->
      
{_, Attrs} = lists:keyfind(attributes, 1, L),
      
{_, Vsn} = lists:keyfind(vsn, 1, Attrs),
      
Vsn.
  
```

모듈의 버전은 다음 매크로를 통해 관리할 수 있다.

```erlang
  
-vsn("1.0").
  
```

변경된 모듈의 리스트를 받아 리로드하는 기능은 다음과 같이 구현한다.

```erlang
  
reload_modules(Modules) ->
      
[begin code:purge(M), code:load_file(M) end || M <- Modules].
  
```

## 참고

  * [mochiweb reloader.erl](https://github.com/mochi/mochiweb/blob/1.4.0/src/reloader.erl)