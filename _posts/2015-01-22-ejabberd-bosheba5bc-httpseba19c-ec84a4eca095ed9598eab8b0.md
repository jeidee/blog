---
id: 38
title: ejabberd bosh를 https로 설정하기
date: 2015-01-22T00:00:33+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/22/ejabberd-bosh%eb%a5%bc-https%eb%a1%9c-%ec%84%a4%ec%a0%95%ed%95%98%ea%b8%b0/'
permalink: '/2015/01/22/ejabberd-bosh%eb%a5%bc-https%eb%a1%9c-%ec%84%a4%ec%a0%95%ed%95%98%ea%b8%b0/'
categories:
  - ejabberd
tags:
  - bosh
  - https
---
ejabberd에서 bosh를 설정하기 위해서는 다음과 같이 ejabberd.yml에서 ejabberd_http 모듈을 설정해야 한다.

```
  
&#8211;
    
port: 5280
    
module: ejabberd_http
    
request_handlers:
      
"/hello": mod\_http\_hello
      
"/api": mod\_http\_api
    
web_admin: true
    
http_poll: true
    
http_bind: true
    
\## register: true
    
captcha: true
  
```

위와 같이 설정할 경우 admin, api, bosh가 모두 http://localhost:5280 하에서 동작하게 되는데 https로 설정하기 위해서는 다음과 같이 5223 tls 설정과 유사하게 설정하면 된다.

```
  
&#8211;
    
port: 5281
    
module: ejabberd_http
    
certfile: "/path/to/server.pem"
    
tls: true
    
http_bind: true
  
```

주의할 것은 tls 설정항목이 starttls가 아니라 tls라는 것이다. 5223 tls 설정에서는 starttls: true라고 설정해야 하지만 https를 위해서는 tls: true로 설정해야 한다.

위와 같이 설정한 후 bosh를 지원하는 xmpp client에서 다음과 같이 boshurl을 설정한 후 로그인할 수 있다.

```
      
https://localhost:5281/http-bind
  
```