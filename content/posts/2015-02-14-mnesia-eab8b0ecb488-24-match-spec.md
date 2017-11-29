---
author: jeidee
categories:
- erlang
date: '2015-02-14'
guid: https://erlnote.wordpress.com/?p=170
id: 170
permalink: /2015/02/14/mnesia-%ea%b8%b0%ec%b4%88-24-match-spec/
tags:
- mnesia
title: mnesia 기초 2/4 &#8211; Match Spec
url: /2015/02/14/mnesia-eab8b0ecb488-24-match-spec
---

# Match Spec

mnesia:select 또는 mnesia:dirty_select에 Match Spec을 정의해서 데이터를 검색할 수 있다.
  
MatchSpec의 기본형은 다음과 같다.

```erlang
      
[{ {매치패턴}, [조건식], [결과값]}]
  
```

매치패턴은 튜플의 형태로 테이블의 레코드의 구조와 동일해야한다.

```erlang
      
[{ {'$1', '$2', &#8230;}, [], &#8230;
  
```

$1, $2는 바인드 변수이며 Match Spec안에서만 사용된다.

조건식은 다음과 같이 논리연산자, 비교컬럼지정자, 검색값 형식으로 입력한다.

```erlang
      
['>', '$1', "abcd"]
  
```

예를 들면 다음과 같다.

```erlang
      
-type my_color() :: red | yellow | blue;
      
-record(shape, {name = "" :: string(), color = red :: fruit_color(), count = 0 :: integer()}).

init() ->
          
mnesia:create_table(shape,
              
[{disc_copies, [node()]},
              
{attributes, record_info(fields, shape)}]),
          
F = fun() ->
              
mnesia:write(#shape{name = "rect1", color = red, count = 1}),
              
mnesia:write(#shape{name = "rect2", color = yellow, count = 10}),
              
mnesia:write(#shape{name = "circle", color = blue, count = 5})
              
end,
          
{atomic, ok} = mnesia:transaction(F).

select_rect() ->
          
mnesia:dirty\_select(shape, [{ {shape, "rect" ++ '\_', '\_', '\_'}, [], ['$_'] }]).

select_red() ->
          
mnesia:dirty_select(shape, [{ {shape, '$1', red, '$2'}, [], [{'$1', '$2'}] }]).

select_count({greater, Count}) ->
          
mnesia:dirty_select(shape, [{ {shape, '$1', '$2', '$3'}, ['>', '$3', Count], [{'$1', '$2', '$3'}] }]).
  
```