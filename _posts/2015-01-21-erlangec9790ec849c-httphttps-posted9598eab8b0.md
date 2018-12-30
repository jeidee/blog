---
author: jeidee
categories:
- erlang
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/erlang%ec%97%90%ec%84%9c-httphttps-post%ed%95%98%ea%b8%b0/
id: 11
permalink: /2015/01/21/erlang%ec%97%90%ec%84%9c-httphttps-post%ed%95%98%ea%b8%b0/
title: erlang에서 http(https)  post하기
url: /2015/01/21/erlangec9790ec849c-httphttps-posted9598eab8b0
---

http(https) post하는 대표적인 방법은 urlencoded방식과 json방식이 있다.

가장 보편적으로 사용되는 urlencoded 방식의 코드는 다음과 같다.

```erlang
      
post_test1() ->
          
Method = post,
          
URL = "https://www.google.co.kr/search",
          
Header = [],
          
Type = "application/x-www-form-urlencoded",
          
Body = "q=erlang+http+post+example",
          
HTTPOptions = [{ssl, [{verify, 0}]}],
          
Options = [],
          
R = httpc:request(Method, {URL, Header, Type, Body}, HTTPOptions, Options),
          
io:format("~p", [R]),
          
ok.
  
```

json 방식은 다음과 같이 Type을 "application/json"으로 설정하고 , Body를 json 데이터로 입력해서 post하면 된다.

```erlang
      
post_test2() ->
          
Method = post,
          
URL = "https://www.google.co.kr/search",
          
Header = [],
          
Type = "application/json",
          
Body = "{\"q\":\"erlang+http+post+example\"",
          
HTTPOptions = [{ssl, [{verify, 0}]}],
          
Options = [],
          
R = httpc:request(Method, {URL, Header, Type, Body}, HTTPOptions, Options),
          
io:format("~p", [R]),
          
ok.
  
```

하지만 google search는 GET 메소드만 지원하기 때문에 위의 코드는 제대로된 결과를 받지 못할 것이다.