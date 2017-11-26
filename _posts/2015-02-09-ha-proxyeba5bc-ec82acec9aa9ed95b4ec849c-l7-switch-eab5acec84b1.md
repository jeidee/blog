---
id: 149
title: HA proxy를 사용해서 L7 switch 구성
date: 2015-02-09T16:55:35+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/02/09/ha-proxy%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-l7-switch-%ea%b5%ac%ec%84%b1/'
permalink: '/2015/02/09/ha-proxy%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-l7-switch-%ea%b5%ac%ec%84%b1/'
categories:
  - utility
tags:
  - haproxy
---
## 다음과 같이 HA Proxy를 구성하고 싶다고 가정하자.

  * A 서버 : 웹서버 
      * http://192.168.56.101:3000/ : 홈
      * http://192.168.56.101:3000/admin : 관리 &#8230;
  * B 서버 : RESTful API 서버 
      * http://192.168.56.102:5281/api
  * C 서버 : HA Proxy 서버 
      * https://192.168.56.103/ => http://192.168.56.101:3000/
      * https://192.168.56.103/admin => http://192.168.56.101:3000/admin
      * https://192.168.56.103/api => http://192.168.56.102:5281/api

네트워크 토폴로지는 다음과 같다.

```
      
+&#8212;&#8212;&#8212;&#8212;+ https +&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
      
| Client | &#8212;&#8212;>| HA Proxy |
      
| | | 192.168.56.103 |
      
+&#8212;&#8212;&#8212;&#8212;+ +&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
                                      
| http
                                      
V
                            
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
                    
+&#8212;&#8212;-| fire wall |
                    
| +&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
                    
| | http
                    
| V
                    
| +&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;+
                    
| | 192.168.56.101:3000 |
                    
| | /, /admin, /doc, &#8230; |
                    
| +&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;+
                    
|
                    
V
      
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;+
      
| 192.168.56.102:5281/api |
      
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;+
  
```

## HA Proxy 설정 시나리오는 다음과 같다.

  * 192.168.56.103 서버에 HA Proxy를 설치한다.
  * frontend 구성에 https를 위한 ssl 설정을 하고(SSL Key 필요),
  * /api url을 제외한 기본 백엔드는 192.168.56.101:3000 서버를 지정하고
  * /api url의 경우 192.168.56.102:5281 서버를 지정하도록 한다.

1) HA Proxy 설치

```
      
$ sudo apt-get install haproxy
  
```

2) 설정

```
      
$ sudo vi /etc/haproxy/haproxy.cfg
  
```

haproxy.cfg의 뒷부분에 다음을 추가하도록 한다.

```
      
frontend www-https
        
bind *:443 ssl crt /home/gss.dev/server/bin/etc/ejabberd/server.pem
        
mode http
        
option httpclose
        
option forwardfor
        
reqadd X-Forwarded-Proto: https
        
acl url\_api path\_beg /api
        
use\_backend www-api if url\_api
        
default_backend www-backend

backend www-backend
        
mode http
        
server www-1 192.168.56.101:3000 check

backend www-api
        
mode http
        
server apidoc 192.168.56.102:5281 check
  
```

주의할 것은 ssl 설정을 HA Proxy서버에서 한다는 것이다. 백엔드 서버는 http로 구성해야 하며 https로 구성할 경우 Bad Gateway가 떨어진다.

## References

  * [How To Use HAProxy As A Layer 7 Load Balancer For WordPress and Nginx On Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-use-haproxy-as-a-layer-7-load-balancer-for-wordpress-and-nginx-on-ubuntu-14-04)
  * [Using SSL/HTTPS with HAProxy](http://seanmcgary.com/posts/using-sslhttps-with-haproxy)