---
id: 19
title: erlang/otp application
date: 2015-01-21T23:35:45+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/2015/01/21/erlangotp-application/
permalink: /2015/01/21/erlangotp-application/
categories:
  - erlang
---
## application concept

어떤 특정 기능을 수행하는 코드를 작성할 때, 우리는 다른 시스템에서 재사용될 수 있으며, 유닛으로써 시작과 중지할 수 있는 컴포넌트인 application에 코드를 작성할 수 있길 원한다.

이것을 위해 Erlang/OTP에서는 application callback module을 제공한다. application이 어떻게 시작되고 중지될 수 있는지에 대해서는 application callback moudule과 함께 설명하도록 하겠다.

application 명세는 application resource file에 포함하는데, 해당 명세에는 application을 구성하는 모듈과 callback module의 이름이 명시되어 있다.

코드 패키징을 위한 Erlang/OTP 도구인 systool을 사용한다면(자세한 내용은 [Release](http://www.erlang.org/doc/design_principles/release_structure.html)참조) , 각각의 application 코드들은 미리 정의된 디렉토리 구조(뒤에 설명함)를 따르는 구분된 디렉토리에 위치해야 한다.

## application callback module

application을 시작하고 중지하기 위해서는(예를 들어 supervison tree에서) 다음의 두 callback 함수가 필요하다.

```erlang
      
start(StartType, StartArgs) -> {ok, Pid} | {ok, Pid, State}
      
stop(State)
  
```

start/2함수는 application이 시작할때 호출되며 상위 supervisor를 시작하는 supervision tree에 의해 생성되어야 한다. start/2 함수는 top supervisor의 pid를 반환해야 하고 선택적으로 State term([] 기본값을 갖는)도 반환할 수 있다. 이 term은 stop/1 함수 매개변수로 넘겨야 한다.

StartType은 일반적으로 normal 애텀이 사용된다. [Distributed Applications](http://www.erlang.org/doc/design_principles/distributed_applications.html)에서 설명된 takeover와 faileover의 경우에만 다른 값을 사용할 수 있다. StartArgs는 application resource file에 명시된 mod의 key에 의해 정의된다.

stop/1함수는 application이 중지된 후에 호출되며 필요한 해제절차를 수행해야 한다. 실제 application의 중지는 supervision tree가 종료될 때 자동적으로 처리되는데 자세한 내용은 뒷부분에서 자세히 설명하도록 한다.

## application resource file

application을 정의하기 위해서 application resource file(.app file)에 application 명세를 작성해야 한다.

```erlang
      
{application, Application, [Opt1,&#8230;,OptN]}.
  
```

Application 부분에는 application의 이름을 애텀으로 작성한다. 파일명은 Application.app(즉, application의 애텀명.app 형태)이어야 한다.

각각의 Opt는 {Key, Value} 튜플이고, application의 특정 프로퍼티를 정의한다.

예를 들어 library application인 libapp을 위한 최소한의 .app 파일은 다음과 같을 것이다.

```erlang
      
{application, libapp, []}.
  
```

supervision tree application의 최소버전인 ch_app.app의 최소한의 내용은 다음과 같다.

```erlang
      
{application, ch_app,
       
[{mod, {ch_app,[]}}]}.
  
```

mod 키는 callback module과 application의 start 매개변수를 정의한다. 이 경우에 callback module은 ch_app이고 start의 매개변수는[] 이다.
  
이것이 의미하는 바는 다음과 같다.

```erlang
      
ch_app:start(normal, [])
  
```

application이 시작할 때 위와 같이 호출되고,

```erlang
      
ch_app:stop([])
  
```

application이 중지될 때 위와 같이 호출된다.

systools를 사용해서 패키징할 때는 description, vsn, modules, registered, applications 키가 다음과 같이 명시되어야 한다.

```erlang
      
{application, ch_app,
       
[{description, "Channel allocator"},
        
{vsn, "1"},
        
{modules, [ch\_app, ch\_sup, ch3]},
        
{registered, [ch3]},
        
{applications, [kernel, stdlib, sasl]},
        
{mod, {ch_app,[]}}
       
]}.
  
```

  * description : 기본값을 &#8220;&#8221;로 설정할 수 있는 짧은 문자열로 된 설명
  * vsn : 기본값을 &#8220;&#8221;로 설정할 수 있는 버전 문자열
  * modules : application에서 소개될 모든 모듈들. systools는 boot scripts와 tar files를 생성할 때 이 리스트를 사용하게 된다. 모듈은 오직 하나만 정의해야 한다. 기본값은 []이다.
  * registered : application에서 등록된 process의 이름 리스트이다. systools는 이 리스트를 통해 application들 사이에서 이름 충돌을 찾는다. 기본값은 []이다.
  * applications : 이 application이 시작될 대 함께 시작되어야 하는 application의 목록이다. systools는 이 리스트를 사용해 올바른 boot scripts를 작성한다. 기본값은 []이지만, 모든 application은 최소한 kernel과 stdlib에 의존성이 있음을 알아야 한다.

> 좀더 자세한 내용은 [Application resource file reference](http://www.erlang.org/doc/man/app.html)을 참고하도록 한다. 

## directory structure

systools를 사용해 패키징할 때, 각 application의 코드는 lib/Application-Vsn의 구분된 디렉토리에 위치해야 한다.

systools를 사용하지 않더라도 이러한 방식은 유용하며 Erlang/OTP 자체가 이러한 OTP 원칙에 의해 패키징되어 있다. code 서버는 자동적으로 취상위 버전의 디렉토리에 있는 코드를 사용할 것이다.

application 디렉토리 구조는 개발환경에서도 사용할 수 있으며 이때는 버전 번호를 생략할 수 있다.

application 디렉토리는 다음과 같은 서브 디렉토리를 갖는다.

  * src : erlang source code
  * ebin : erlang object code(beam files) and .app file
  * priv : application specific files(예를 들어 c 실행파일, code:priv_dir/1함수는 이 경로를 접근한다.)
  * include : includes files(.hrl)

## application controller

## references

  * [Erlang/OTP Applications](http://www.erlang.org/doc/design_principles/applications.html#appl_res_file)