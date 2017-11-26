---
id: 30
title: ejabberd roster 조회 api 만들기
date: 2015-01-21T23:51:10+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/ejabberd-roster-%ec%a1%b0%ed%9a%8c-api-%eb%a7%8c%eb%93%a4%ea%b8%b0/'
permalink: '/2015/01/21/ejabberd-roster-%ec%a1%b0%ed%9a%8c-api-%eb%a7%8c%eb%93%a4%ea%b8%b0/'
categories:
  - ejabberd
tags:
  - roster
---
## process handler 추가

[mod\_http\_api.erl](https://erlnote.wordpress.com/2015/01/21/ejabberd-http-request-handlers/)을 작성했다고 가정한다.

request handler에 다음과 같은 코드를 추가한다.

```erlang
      
process([<<"friend">>, <<"list">>], _Request) ->
        
try get\_friend\_list(_Request)
        
catch
          
throw: {Message, Result} -> {200, ?OPTIONS\_HEADER, list\_to_json(Message, Result)};
          
\_:\_ -> {200, ?OPTIONS\_HEADER, list\_to_json(<<"An error occured during the requested work.">>, <<"500">>)}
        
end;
  
```

mod\_http\_api 모듈이 /api에 바인딩되어 있다면 위의 request 핸들러는 다음과 같은 url을 처리하게 된다.

```
      
/api/friend/list
  
```

try&#8230;catch 블럭은 get\_friend\_list/1에서 예외가 발생할 경우 catch해 올바른 포맷의 결과를 클라이언트에 전달할 수 있도록 해준다.
  
만약 try&#8230;catch를 하지 않을 경우 해당 요청 처리 중에 예외가 발생하면 클라이언트는 아무런 결과도 받지 못하게 된다.

## get\_friend\_list/1

실제로 roster를 조회하는 get\_friend\_list/1 함수는 다음과 같다.

```erlang
      
get\_friend\_list(_Request) ->
        
%%?DEBUG("get\_friend\_list() \_Request is ~p~n", [\_Request]),
        
Args = element(4, _Request),
        
check_ticket(Args),
        
User = proplists:get_value(<>, Args),
        
Server = proplists:get_value(<>, Args),
        
?DEBUG("==== prameter User ~p, Server ~p ~n",
          
[User, Server]),
        
if
          
User == undefined;
          
Server == undefined ->
            
throw({<>, <>});
          
true -> ok
        
end,
        
Rosters = mod\_admin\_extra:get_roster(User, Server),
        
if length(Rosters) == 0 ->
          
throw({<>, <>});
          
true -> ok
        
end,
        
% JSON Document로 변환
        
Result = [{[{jid, Jid}, {subscription, list\_to\_binary(Subscription)}]} || {Jid, \_, Subscription, \_, _} <- Rosters],
        
{200, ?OPTIONS\_HEADER, list\_to_json(value, Result, <>, <>)}.
  
```

check_ticket/1함수는 유효한 API인지 검사하기 위한 internal 함수인데 생략하도록 한다.

함수의 flow를 개략적으로 살펴보면 다음과 같다.

  1. _Request에서 query string 분리
  2. query string에서 파라미터 분리
  3. 파라미터 유효성 검사
  4. mod\_admin\_extra:get_roster/2 함수를 사용해 roster 조회
  5. roster가 비어 있을 경우 예외 발생
  6. roster를 JSON으로 encoding하기 위해 리스트해석을 통해 EJSON term으로 변경
  7. list\_to\_json/4함수를 사용해 EJSON term을 JSON으로 변경해서 클라이언트에 반환

위 flow에서 중요한 부분은 4번과 6번이다.
  
jiffy의 encode/1 함수에 입력되는 변수는 반드시 다음 포맷과 일치해야 한다.

```
      
{[ {}, {}, &#8230; ]}
  
```

즉 term의 리스트를 요소로 갖는 term이어야 한다.
  
내부의 term({}) 리스트에는 JSON으로 변환되었을 때 key: value의 값이 되기 위해 두 개의 요소를 입력하면 된다.
  
예는 다음과 같다.

```
      
{[ {name, <>}, {age, 10} ]}
  
```

위의 입력 값은 다음과 같이 변환된다.

```
      
{
          
name: "Juliet",
          
age: 10
      
}
  
```