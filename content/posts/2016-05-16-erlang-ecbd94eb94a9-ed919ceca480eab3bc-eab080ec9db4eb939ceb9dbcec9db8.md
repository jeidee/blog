---
author: jeidee
categories:
- erlang
date: '2016-05-16'
guid: https://erlnote.wordpress.com/?p=1121
id: 1121
permalink: /2016/05/16/erlang-%ec%bd%94%eb%94%a9-%ed%91%9c%ec%a4%80%ea%b3%bc-%ea%b0%80%ec%9d%b4%eb%93%9c%eb%9d%bc%ec%9d%b8/
title: Erlang 코딩 표준과 가이드라인
url: /2016/05/16/erlang-ecbd94eb94a9-ed919ceca480eab3bc-eab080ec9db4eb939ceb9dbcec9db8
---

## 참고원문

https://github.com/inaka/erlang_guidelines

자세한 내용은 원문을 참고하세요.

## 소스 코드 레이아웃

  * 탭은 공백문자 2개로 표현하며 탭문자를 사용하지 않는다. _Examples_: [indent](src/indent.erl)
  * 연산자는 공백으로 감싼다. _Examples_: [spaces](src/spaces.erl)
  * 줄의 끝에서 Whitespace를 제거한다. _Examples_: [trailing_whitespace](src/trailing_whitespace.erl)
  * 한 줄에 작성하는 문자는 80 컬럼을 넘지 않는다. _Examples_: [col_width](src/col_width.erl)
  * 기존 코드의 스타일을 준수한다. _Examples_: [existing_style](src/existing_style.erl)
  * 깊은 중첩을 피한다. _Examples_: [nesting](src/nesting.erl)
  * case 표현식보다는 함수 패턴 매치를 사용한다.
  
    _Examples_: [smaller_functions](src/smaller_functions.erl)
  * public(exported)/private(unexported) 함수를 논리적으로 그룹짓는다. _Examples_: [grouping_functions](src/grouping_functions)
  * 사용자정의 타입은 소스파일의 상단에 모아 놓는다. _Examples_: [type_placement](src/type_placement.erl)
  * 모든 기능을 하는 신의 모듈(슈퍼모듈)을 만들지 않는다. _Examples_: [god](src/god.erl)
  * 유닛 테스트는 간단하게(가능한 짧고 assert문은 한 개 또는 두 개만) 만든다. _Examples_: [test_SUITE](src/test_SUITE.erl)
  * DRY(Don&#8217;t repeat yourself) 원칙을 준수한다.(중복코드 제거) _Examples_: [dry](src/dry.erl)
  * 동적 호출을 지양한다.
  
    _Examples_: [dyn_calls](src/dyn_calls.erl)
  * 기능적으로 유사한 모듈은 서브디렉토리를 통해 그룹짓는다.(선택) Emakefile 참조할 것!
  * 스파게티 코드(가독성을 헤치는 복잡한 코드)를 작성하지 마라. _Examples_: [spaghetti](src/spaghetti.erl)

## 구문

  * if 구문을 사용하지 말고 함수 패턴 매치를 사용해라. _Examples_: [no_if](src/no_if.erl)
  * 중첩 try&#8230;catch 절을 사용하지 마라. _Examples_: [nested\_try\_catch](src/nested_try_catch.erl)

## 네이밍

  * 네이밍 컨셉은 일관성을 유지해라. _Examples_: [consistency](src/consistency.erl)
  * OTP 모듈의 상태(State)는 state 레코드(-type state():: #state{})를 정의해서 사용한다. _Examples_: [state](src/state)
  * _Ignored(사용하지 않아도 컴파일 에러에서 무시됨) 변수를 사용하지 않는다. \*Examples\*: [ignored_vars](src/ignored_vars.erl)
  * 흐름제어를 위한 boolean 매개변수를 사용하지 않는다. _Examples_: [boolean_params](src/boolean_params.erl)
  * 모듈의 네이밍은 동일한 규칙을 적용한다. _Examples_: [naming_modules](src/naming_modules)
  * 특수한 문자열 상수는 소문자 형식의 atom을 사용한다. _Examples_: [atoms](src/atoms.erl)
  * 함수이름은 단어사이를 _로 구분한 소문자만으로 작명한다. \*Examples\*: [function_names](src/function_names.erl)
  * 변수이름은 CamelCase를 사용하며 단어를 구분하기 위해 _를 사용하지 않는다. \*Examples\*: [variable_names](src/variable_names.erl)

## 문자열

  * 문자열 연결을 할 때 ++ 사용하는 대신, iolists를 사용해라. _Examples_: [iolists](src/iolists.erl)

## 매크로

  * 이미 정의된(?MODULE, ?MODULE_STRING, ?LINE&#8230;) 매크로 이외의 매크로를 사용하지 마라. _Examples_: [macros](src/macros.erl)
  * 매크로는 대문자만으로 네이밍한다. _Examples_: [macro_names](src/macro_names.erl)
  * 매크로 이름에 함수나 모듈의 이름을 사용하지 않는다. _Examples_: [macro\_mod\_names](src/macro_mod_names.erl)

## 레코드

  * 레코드 이름은 소문자로만 작성하며 단어사이는 _로 구분한다. \*Examples\*: [record_names](src/record_names.erl)
  * 레코드는 함수 정의부보다 항상 먼저 정의되어야 한다. _Examples_: [record_placement](src/record_placement.erl)
  * 레코드를 .hrl 파일을 통해 다른 모듈과 공유하지 않는다. 필요하다면 opaque 키워드를 통해 다른 모듈로 타입을 export할 수 있다. _Examples_: [record_sharing](src/record_sharing.erl)
  * spec을 정의할 때 레코드를 사용하는 대신 타입을 사용해야 한다. _Examples_: [record_spec](src/record_spec.erl)
  * 레코드의 필드는 항상 타입 정의를 추가해서 작성한다. _Examples_: [record_types](src/record_types.erl)

## 기타

  * 함수의 spec을 항상 작성한다. _Examples_: [specs](src/specs.erl)
  * erlang R14 이전의 컴파일러를 지원하기 위해 behaviour_info/1 대신 -callback 지시자를 사용해라. _Examples_: [callbacks](src/callbacks)
  * 중첩된 헤더파일 포함 구문을 사용하지 마라.
  
    _Examples_: [nested](include/nested.hrl)
  * 헤더파일에 -type 지시자를 사용하지 않는다.
  
    _Examples_: [types](src/types.erl)
  * -import 지시자를 사용하지 마라. _Examples_: [import](src/import.erl)
  * -export_all 지시자를 사용하지 마라.
  
    _Examples_: [export_all](src/export_all.erl)
  * 디버깅을 위한 호출을 사용하지 마라.(특히 로그를 남기기 위한 코드는 성능에 나쁜 영향을 끼친다.) _Examples_: [debug_calls](src/debug_calls.erl)

## 도구

  * 의존성 모듈을 포함할 때는, master 대신 특정 tag나 커밋버전을 명시한다. _Examples_:
  * [erlang.mk](priv/Makefile)
  * [rebar.config](priv/rebar.config)
  * 에러가 발생하면 로그로 출력해라. _Examples_: [loud_errors](src/loud_errors.erl) 
  * 다음과 같이 적합한 로그레벨을 사용해라.
  
    ** debug: 매우 저수준의 정보를 출력
  
    ** info: 시스템의 약간 상세한 정보를 출력한다. 일반적이지만 항상 발생하지 않으며 받아드릴 수 있는 수준이어야 한다.
  
    ** notice: 알릴 가치가 있는 정보(supervisor, gen_server등의 시작, 종료)를 출력할 때 사용한다.
  
    ** warning: 시스템이 계속 동작하는데 영향을 주진 않지만 정상적인 경우가 아닌 에러를 처리할 때 사용한다.
  
    ** error: 무언가 나쁘고 예상치 못한 상황이 발생할 때, 보통 예외나 에러가 발생할 때 사용하며 스택트레이스를 함께 출력해야 한다.
  
    ** critical: 시스템 크래시 상황에 사용한다.
  * 의존성 모듈을 명시할 때, https 이외의 것은 사용하지 않는다. _Examples_: [makefile example](src/dependency_protocol/dep_protocol.makefile) [rebar example](src/dependency_protocol/dep_protocol.config)

## 제안 및 훌륭한 아이디어

  * 변수는 _대신 CamelCase를 사용한다. \*Examples\*: [camel_case](src/camel_case.erl)
  * 의미를 알 수 있는 짧은 변수명을 사용한다. _Examples_: [var_names](src/var_names.erl)
  * 모듈 주석은 %%%, 함수 주석은 %%, 문장 코드 주석은 %를 사용한다. _Examples_: [comment_levels](src/comment_levels.erl)
  * 함수는 가능한 짧게 유지한다. _Examples_: [small_funs](src/small_funs.erl)
  * 재사용 가능한 캡슐화된 코드를 위해 behaviour를 사용한다. _Examples_: [behavior](src/behavior.erl)
  * 방어적인 프로그래밍은 클라이언트 코드에서 작성해라. (유효성 체크는 코드의 바깥에서) _Examples_: [validations](src/validations.erl)
  * 빈 리스트 검사를 위해 length/1를 사용하는 대신 함수 패턴 매치를 사용해 빈리스트와 매치해서 사용해라. _Examples_: [pattern matching](src/pattern_matching.erl)
  * 독립적인 애플리케이션은 옮겨 작성하는 것을 고려해라.(가능하면 오픈소스 형태로&#8230;) 
  * 라이브러리는 facade 패턴을 사용해라. _Examples_: [kafkerl](https://github.com/inaka/kafkerl/blob/master/src/kafkerl.erl)
  * export된 함수에서 사용하는 사용자 정의 타입은 해당 모듈에서 정의해서 함께 export하도록 한다. _Examples_: [data_types](src/data_types.erl)