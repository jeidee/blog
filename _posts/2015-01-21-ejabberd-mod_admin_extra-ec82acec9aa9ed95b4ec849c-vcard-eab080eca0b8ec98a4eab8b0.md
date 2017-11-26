---
id: 31
title: ejabberd mod_admin_extra 사용해서 vcard 가져오기
date: 2015-01-21T23:52:52+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/ejabberd-mod_admin_extra-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-vcard-%ea%b0%80%ec%a0%b8%ec%98%a4%ea%b8%b0/'
permalink: '/2015/01/21/ejabberd-mod_admin_extra-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-vcard-%ea%b0%80%ec%a0%b8%ec%98%a4%ea%b8%b0/'
categories:
  - ejabberd
tags:
  - vcard
---
ejabberd에서는 커스텀 플러그인 모듈을 다음 URL에서 별도로 배포하고 있다.
  
https://www.ejabberd.im/ejabberd-modules

하지만 일부 모듈의 경우 최신 버전에서 사용할 수 없고, 수정된 모듈에서도 이전 버전과 인터페이스 호환이 되지 않아 문제를 일으키는 경우가 있으므로 주의해야 한다.

## mod\_admin\_extra 모듈을 사용해 ejabberdctl의 get_vcard 코맨드 실행

ejabberd-modules 중에서 mod\_ctlextra와 mod\_admin\_extra 모듈은 ejabberdctl용(관리자 콘솔 코맨드) 확장 코맨드 모듈이다. 둘 중에서 mod\_admin\_extra가 최신 버전이지만 최신 버전의 ejabberd(현재 기준 14.07)에서 사용되는 gen\_mod:get\_module\_opt/5와 호환되지 않아(mod\_admin\_extra는 gen\_mod:get\_module_opt/4를 사용) 일부 코드를 수정해야 한다.

```erlang
       
963 get\_module\_resource(Server) ->
       
964 case gen\_mod:get\_module\_opt(Server, ?MODULE, module\_resource, none) of
       
965 none -> atom\_to\_list(?MODULE);
       
966 R when is_list(R) -> R
       
967 end.
  
```

964 라인을 다음과 같이 수정한다.

```erlang
       
964 case gen\_mod:get\_module\_opt(global, Server, ?MODULE, module\_resource, none) of
  
```

그런 후 디버그 쉘(ejabberdctl debug)을 실행하고 다음과 같이 입력한다.

```erlang
      
> mod\_admin\_extra:get\_module\_resource(<<"localhost">>).
      
"mod\_admin\_extra"
  
```

위와 같이 출력되면 이상이 없는 것이다.

이 외에도 get\_vcard와 get\_vcard_content 함수를 다음과 같이 수정한다.

```erlang
      
get_vcard(User, Host, Name) ->
          
if
              
is\_list(User) -> U = list\_to_binary(User);
              
true -> U = User
          
end,
          
if
              
is\_list(Host) -> H = list\_to_binary(Host);
              
true -> H = Host
          
end,
          
if
              
is\_list(Name) -> N = list\_to_binary(Name);
              
true -> N = Name
          
end,
          
[Res | \_] = get\_vcard_content(U, H, [N]),
          
Res.

get_vcard(User, Host, Name, Subname) ->
          
if
              
is\_list(User) -> U = list\_to_binary(User);
              
true -> U = User
          
end,
          
if
              
is\_list(Host) -> H = list\_to_binary(Host);
              
true -> H = Host
          
end,
          
if
              
is\_list(Name) -> N = list\_to_binary(Name);
              
true -> N = Name
          
end,
          
if
              
is\_list(Subname) -> SN = list\_to_binary(Subname);
              
true -> SN = Subname
          
end,
          
[Res | \_] = get\_vcard_content(U, H, [N, SN]),
          
Res.

get\_vcard\_content(User, Server, Data) ->
          
[{\_, Module, Function, \_Opts}] = ets:lookup(sm\_iqtable, {?NS\_VCARD, Server}),
          
JID = jlib:make\_jid(User, Server, list\_to\_binary(get\_module_resource(Server))),
          
IQ = #iq{type = get, xmlns = ?NS_VCARD},
          
IQr = Module:Function(JID, JID, IQ),
          
case IQr#iq.sub_el of
          
[A1] ->
              
case get_vcard(Data, A1) of
              
[] -> throw(error\_no\_value\_found\_in_vcard);
              
ElemList -> [xml:get\_tag\_cdata(Elem) || Elem <- ElemList]
              
end;
          
[] ->
              
throw(error\_no\_vcard_found)
          
end.
  
```

그런 후 소스를 재컴파일 & 재배포(make && make install)한 후 ejabberd를 실행한다.
  
쉘(erl 쉘이 아니다.)에서 다음과 같이 실행해 보자.

```
      
$ ejabberdctl get_vcard "romeo" "localhost" "NICKNAME"
  
```

만약 romeo@localhost의 vcard가 있고 NICKNAME이 설정되어 있다면 정상적으로 출력될 것이다.

이제 제대로 작동하는 것을 확인했다면 소스를 살펴보도록 한다.

## mod\_admin\_extra의 get_vcard/3 관련 소스 살펴보기

mod\_admin\_extra모듈은 vcard 관련 작업을 할 때 mod\_vcard모듈의 process\_sm_iq/3 함수를 호출하는데, 이 때 세 번째 파라미터로 사용하는 Iq의 경우 레코드타입이므로 쉘에서 테스트해보고자 한다면 #iq레코드가 정의되어 있어야 한다.

xml.hrl과 jlib.hrl을 rr() 코맨드로 load해서 레코드를 등록해도 되지만, 어떤 이유에서인지 jlib.hrl은 xmlel 레코드가 정의되어 있지 않다는 에러를 내뿜으며 제대로 불러지지 않는다.

따라서 다음과 같이 직접 erl 쉘에서 레코드를 등록해 사용하도록 하자.

```erlang
      
rd(xmlel, {name= <<"">>, attrs= [], children= []}).
      
rd(iq, {id= <<"">>, type=get, xmlns= <<"">>, lang= <<"">>, sub_el= #xmlel{}}).
  
```

주의할 것은 바이너리 값(<>)을 매치연산자(=)와 같이 사용할 때는 꼭 = < 그 전에, erl 쉘에서 테스트를 용이하게 하기 위해 mod\_admin\_extra의 모든 함수를 export하자.(기존 -export 부분을 주석처리하고 -compile(export_all). 사용)

mod\_admin\_extra모듈에는 get_vcard가 다음과 같이 정의되어 있다.

```erlang
      
get_vcard(User, Host, Name) ->
          
[Res | \_] = get\_vcard_content(User, Host, [Name]),
          
Res. 

get_vcard(User, Host, Name, Subname) ->
          
[Res | \_] = get\_vcard_content(User, Host, [Name, Subname]),
          
Res.
  
```

get\_vcard\_content/3 함수를 사용하는데 해당 함수는 다음과 같다.

```erlang
      
get\_vcard\_content(User, Server, Data) ->
          
[{\_, Module, Function, \_Opts}] = ets:lookup(sm\_iqtable, {?NS\_VCARD, Server}),
          
JID = jlib:make\_jid(User, Server, list\_to\_binary(get\_module_resource(Server))),
          
IQ = #iq{type = get, xmlns = ?NS_VCARD},
          
IQr = Module:Function(JID, JID, IQ),
          
case IQr#iq.sub_el of
          
[A1] ->
              
case get_vcard(Data, A1) of
              
[] -> throw(error\_no\_value\_found\_in_vcard);
              
ElemList -> [xml:get\_tag\_cdata(Elem) || Elem <- ElemList]
              
end;
          
[] ->
              
throw(error\_no\_vcard_found)
          
end.
  
```

위의 코드를 보면서, 쉘에서 테스트해 보도록 하자.
  
(xmlel과 iq레코드는 이미 정의했고 ejabberdctl debug로 디버그 쉘을 시작했다고 가정한다.)

먼저 첫 번째 라인을 사용해 ets에서 sm\_iqtable에 저장되어 있는(observer:stat()를 사용해 observer 윈도우를 출력한 후 Table Viewer에서 ets의 sm\_iqtable을 조회해 볼 수 있다.) ?NS\_VCARD(매크로이며 <>값을 갖는다) 네임스페이스의 Server(<>&#8230;)에 해당하는 mod\_vcard 모듈과 process\_sm\_iq함수명을 가져온다.

```erlang
      
> [{\_, Module, Function, \_Opts}] = ets:lookup(sm_iqtable, {<<"vcard-temp">>, <<"localhost">>}).
      
[{ {<<"vcard-temp">>,<<"localhost">>},
        
mod\_vcard,process\_sm_iq,
        
{one_queue,<0.442.0>}}]
  
```

Module은 &#8220;mod\_vcard이고 Function은 process\_sm_iq이다.
  
다음엔 JID를 생성한다.

```erlang
      
(ejabberd@localhost)14> JID = jlib:make\_jid(<<"romeo">>, <<"localhost">>, list\_to\_binary(mod\_admin\_extra:get\_module_resource(<<"localhost">>))).
      
{jid,<<"romeo">>,<<"localhost">>,<<"mod\_admin\_extra">>,
           
<<"romeo">>,<<"localhost">>,<<"mod\_admin\_extra">>}
  
```

그리고 IQ레코드를 생성하고,

```erlang
      
(ejabberd@localhost)18> IQ = #iq{type = get, xmlns = <<"vcard-temp">>}.
      
#iq{id = <<>>,type = get,xmlns = <<"vcard-temp">>,
          
lang = <<>>,
          
sub_el = #xmlel{name = <<>>,attrs = [],children = []}}
  
```

mod\_vcard:process\_sm_iq/3함수를 호출한다.

```erlang
      
(ejabberd@localhost)19> IQr = Module:Function(JID, JID, IQ).
      
#iq{id = <<>>,type = result,xmlns = <<"vcard-temp">>,
          
lang = <<>>,
          
sub_el = [#xmlel{name = <<"vCard">>,
                           
attrs = [{<<"xmlns">>,<<"vcard-temp">>}],
                           
children = [#xmlel{name = <<"NICKNAME">>,attrs = [],
                                              
children = [{xmlcdata,<<"kkk">>}]}]}]}
  
```

A1에 vCard의 xml 엘리먼트를 가져와 담고,

```erlang
      
(ejabberd@localhost)22> [A1] = IQr#iq.sub_el.
      
[#xmlel{name = <<"vCard">>,
              
attrs = [{<<"xmlns">>,<<"vcard-temp">>}],
              
children = [#xmlel{name = <<"NICKNAME">>,attrs = [],
                                 
children = [{xmlcdata,<<"kkk">>}]}]}]
  
```

get_vcard/2 함수를 호출해 NICKNAME과 매치하는 children 요소를 가져오고,

```erlang
      
(ejabberd@localhost)49> ElemList = mod\_admin\_extra:get_vcard([<<"NICKNAME">>], A1).
      
[#xmlel{name = <<"NICKNAME">>,attrs = [],
              
children = [{xmlcdata,<<"kkk">>}]}]
  
```

XML cdata를 구하면 NICKNAME의 값을 가져올 수 있다.
  
(리스트 해석 사용함)

```erlang
      
(ejabberd@localhost)51> [xml:get\_tag\_cdata(Elem) || Elem <- ElemList].
      
[<<"kkk">>]
  
```

다음에는 vcard를 설정하는 코드를 살펴보자.