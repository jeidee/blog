---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-%ea%b0%95%ec%a0%9c%eb%a1%9c-%ed%8a%b9%ec%a0%95-%ec%9c%a0%ec%a0%80-presence-%ec%97%85%eb%8d%b0%ec%9d%b4%ed%8a%b8%ed%95%98%ea%b8%b0/
id: 28
permalink: /2015/01/21/ejabberd-%ea%b0%95%ec%a0%9c%eb%a1%9c-%ed%8a%b9%ec%a0%95-%ec%9c%a0%ec%a0%80-presence-%ec%97%85%eb%8d%b0%ec%9d%b4%ed%8a%b8%ed%95%98%ea%b8%b0/
tags:
- presence
title: ejabberd 강제로 특정 유저 presence 업데이트하기
url: /2015/01/21/ejabberd-eab095eca09ceba19c-ed8ab9eca095-ec9ca0eca080-presence-ec9785eb8db0ec9db4ed8ab8ed9598eab8b0
---

ejabberdctl debug로 쉘을 시작한 후 ejabbrd\_sm 모듈의 force\_update_presence/1 함수를 사용해 특정 유저의 presence를 업데이트할 수 있다.

```erlang
  
(ejabberd@localhost)7> US = {<<"romeo">>,<<"localhost">>}.
  
{<<"romeo">>,<<"localhost">>}

(ejabberd@localhost)9> ejabberd\_sm:force\_update_presence(US).
  
ok
  
```

XMPP 클라이언트에서는 다음과 같이 presence stanza가 갱신되어 수신된다.
  
(swift client의 Debug Console에서 확인)

```
  
<!&#8211; IN &#8211;>
  
<presence from='romeo@localhost/2239860841418983868905790' to='romeo@localhost/2239860841418983868905790'><status/><c xmlns='http://jabber.org/protocol/caps' hash='sha-1' node='http://swift.im' ver='rs/tl9NCfXBpKoOYUy+JdBbPGDg='/></presence><r xmlns='urn:xmpp:sm:2'/>
  
```