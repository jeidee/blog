---
id: 987
title: cowboy에서 SSE를 사용한 웹서버 push 구현하기
date: 2016-02-15T18:03:59+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=987
permalink: '/2016/02/15/cowboy%ec%97%90%ec%84%9c-sse%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%9c-%ec%9b%b9%ec%84%9c%eb%b2%84-push-%ea%b5%ac%ed%98%84%ed%95%98%ea%b8%b0/'
categories:
  - angularjs
  - ejabberd
  - erlang
tags:
  - cowboy
  - SSE
---
[ejabberd에 cowboy를 연동](https://erlnote.wordpress.com/2016/02/03/ejabberd%EC%99%80-cowboy-%EC%97%B0%EB%8F%99/)했다고 가정한다.

Front-end는 AngularJS를 사용한다.

  * [SSE(Server Sent Event)참고](https://www.google.co.kr/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#lr=lang_ko&newwindow=1&tbs=lr:lang_1ko&q=server+sent+event)

클라이언트에서 다음과 같이 EventSource객체를 생성한 후 서버 이벤트를 위한 이벤트리스닝을 시작한다.

https://gist.github.com/jeidee/a135060aef54b48bae1d

서버에서는 다음과 같이 cowboy\_req:chunked\_reply/3 함수를 사용해 content-type이 text/event-stream인 응답을 보내고 {cowboy\_loop, \_, _}를 반환한다.

일반적으로 하나의 클라이언트 요청(http request)당 하나의 erlang process를 생성하며, cowboy\_req:reply/4 함수를 사용해 reponse를 보내고 {ok, \_, _}를 반환하면 해당 erlang process를 종료한다.

하지만 Server Sent Event를 위해 {cowboy\_loop, \_, _}를 반환하면 해당 클라이언트의 요청(Request) 객체를 erlang process에 상태로 유지하며,
  
이 process에 메시지를 보내면(erlang message, Pid ! Message.의 방식으로) cowboy_req:chunk/2 함수를 사용해 해당 클라이언트(erlang process가 유지하고 있는 Request객체를 사요해서)에 지속해서 데이터를 보낼 수 있다.

이 때 cowboy_req:cunk/2에 실어 보내는 Chunk는 문자열포맷으로 &#8220;id:~p\ndata:~s\n\n&#8221;와 같은 구조를 갖고 있어야 한다. data필드는 필수이며, 보통 json 포맷 문자열을 인코딩해 사용한다.
  
좀 더 자세한 내용은 SSE 문서를 참고하도록 한다.

https://gist.github.com/jeidee/71626206d48bf9f1016a

mod\_webadmin\_monitor 모듈은 gen_server를 구현한 서버 모듈이며,
  
mod\_webadmin\_api_handler에서 eventsource를 시작하면 monitor 모듈에 클라이언트 요청 erlang process id를 전달해 추후 서버에서 클라이언트로 푸시할 데이터가 있을 때 PID를 사용해 클라이언트에 데이터를 푸시할 수 있다.

https://gist.github.com/jeidee/e6d89835f7a1706051bf

클라이언트 프로세스가 종료된 경우 해당 PID를 정리해야 하므로 send_data/2 함수에서 해당 Pid가 살아있는지 체크하는 코드가 추가되어 있다.