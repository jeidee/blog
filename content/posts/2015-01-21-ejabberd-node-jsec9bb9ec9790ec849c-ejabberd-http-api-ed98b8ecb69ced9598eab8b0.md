---
author: jeidee
categories:
- ejabberd
- node.js
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-node-js%ec%9b%b9%ec%97%90%ec%84%9c-ejabberd-http-api-%ed%98%b8%ec%b6%9c%ed%95%98%ea%b8%b0/
id: 33
permalink: /2015/01/21/ejabberd-node-js%ec%9b%b9%ec%97%90%ec%84%9c-ejabberd-http-api-%ed%98%b8%ec%b6%9c%ed%95%98%ea%b8%b0/
tags:
- custom module
- mod_http_api
- rest api
title: ejabberd node.js웹에서 ejabberd http api 호출하기
url: /2015/01/21/ejabberd-node-jsec9bb9ec9790ec849c-ejabberd-http-api-ed98b8ecb69ced9598eab8b0
---

## ejabberd rest api를 위한 http 커스텀 모듈

ejabberd 의 http 커스텀 모듈을 mod\_http\_api.erl로 만들었다고 가정한다.
  
(mod\_http\_api.erl 관련 내용은 [이전의 Post](https://erlnote.wordpress.com/2015/01/21/ejabberd-http-request-handlers/)에서 참조)

http://localhost:5280/api/session/list URI를 호출했을 때 동작하는 API를 만들기 위해 mod\_http\_api.erl에 다음과 같이 추가한다.

```erlang
  
process([<<"session">>, <<"list">>], _Request) ->
       
session\_list(\_Request);
  
```

session_list/1 함수는 내부 함수이며 다음과 같다.

```erlang
  
session\_list(\_Request) ->
      
?DEBUG("session\_list() \_Request is ~p~n", [_Request]),
      
List = mod\_admin\_extra:connected\_users\_info(),
      
Y = [X || {[X,\_,\_,\_,\_], \_, \_, \_, \_, \_, \_} <- List],
      
Y1 = {[{list, Y}]},
      
Result1 = jiffy:encode(Y1),
      
Result2 = to_bin("~s", [Result1]),
      
{200, ?OPTIONS_HEADER, Result2}.
  
```

한 줄씩 살펴보자.

(mod\_admin\_extra 모듈은 [이전 Post](https://erlnote.wordpress.com/2015/01/21/ejabberd-mod_admin_extra-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%84%9C-vcard-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/)에서 참조)
  
mod\_admin\_extra모듈의 connected\_users\_info/0 함수는 현재 ejabberd에 접속한 유저 정보를 리스트로 반환한다.

```erlang
  
(ejabberd@localhost)1> mod\_admin\_extra:connected\_users\_info().
  
[{[<>,64,<>,47,
     
<>],
    
"c2s","10.0.2.2",59753,0,"ejabberd@localhost",1278},
   
{[<>,64,<>,47,
     
<>],
    
"c2s","10.0.2.2",59754,0,"ejabberd@localhost",1278}]
  
```

위와 같은 리스트를 갖는데, 이 데이터 중에서 <<"romeo">>와 <<"juliet">>을 얻기 위해 다음과 같이 리스트 해석을 사용한다.

```erlang
  
(ejabberd@localhost)2> List = mod\_admin\_extra:connected\_users\_info().
  
(ejabberd@localhost)3> Y = [X || {[X,\_,\_,\_,\_], \_, \_, \_, \_, \_, \_} <- List].
  
[<>,<>]
  
```

그런 후 jiffy 모듈의 encode/1 함수를 사용해 Json 문서로 인코딩해야 하는데, 그 전에 다음과 같이 해당 리스트를 JSON문서 변환을 위해 EJSON포맷으로 변경해 준다.

```erlang
  
Y1 = {[{list, Y}]},
  
Result1 = jiffy:encode(Y1),
  
Result2 = to_bin("~s", [Result1]),
  
```

to_bin/2 함수는 문자열 포맷팅한 후 바이너리로 변환해 주는 유틸 함수로 erlang 내부 함수가 아님을 주의하자.

마지막으로 response를 돌려 주면 된다.

```
  
{200, ?OPTIONS_HEADER, Result2}.
  
```

?HEADER는 XML 컨텐츠타입이며, ?OPTIONS_HEADER는 palin text 컨텐츠 타입이다.

## node.js에서 ejabberd rest api 호출

다음과 같이 호출한다.

```javascript
  
router.get('/session/list', function(req, res) {
    
//var query = url.parse(req.url, true).query;
    
//debug('get query = ', query)
    
request("http://localhost:5280/api/session/list",
      
function(error, response, body) {
          
data = JSON.parse(body);
          
debug(data.list.length);
          
res.send(data);
      
});
  
});
  
```

웹 브라우저에서 출력하면 다음과 같이 출력된다.

```
  
{"list":["romeo","juliet"]}
  
```