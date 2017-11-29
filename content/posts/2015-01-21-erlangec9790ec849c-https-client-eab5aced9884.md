---
author: jeidee
categories:
- erlang
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/erlang%ec%97%90%ec%84%9c-https-client-%ea%b5%ac%ed%98%84/
id: 10
permalink: /2015/01/21/erlang%ec%97%90%ec%84%9c-https-client-%ea%b5%ac%ed%98%84/
title: erlang에서 https client 구현
url: /2015/01/21/erlangec9790ec849c-https-client-eab5aced9884
---

# 쉘에서 테스트하기

[stackoverflow의 자료](http://stackoverflow.com/questions/2886521/how-do-i-do-an-https-request-with-erlang)를 참고해서 테스트해 보았다.

해당 링크에선 언급하고 있지 않지만 ssl을 시작할 때 오류가 발생할 경우 다음과 같은 순서로 application을 시작해야 한다.

> 주의: asn1에서 1은 숫자 &#8216;1&#8217;이지 영문자 &#8216;l&#8217;이 아니다. 

```erlang
      
application:start(asn1).
      
application:start(public_key).
      
application:start(crypto).
      
application:start(ssl).
      
application:start(inets).
  
```

그런 후 httpc 모듈을 사용해서 url을 request한다.

```erlang
      
httpc:request(head, {"https://github.com/ostinelli/misultin", []}, [{ssl, [{verify, 0}]}], []).
  
```

결과는 다음과 같다.

```erlang
      
{ok,{ {"HTTP/1.1",200,"OK"},
       
[{"cache-control","no-cache"},
        
{"date","Wed, 17 Dec 2014 02:39:06 GMT"},
        
{"server","GitHub.com"},
        
{"vary","X-PJAX"},
        
{"content-type","text/html; charset=utf-8"},
        
{"status","200 OK"},
          
&#8230; (중간 생략)
       
[]}}
  
```