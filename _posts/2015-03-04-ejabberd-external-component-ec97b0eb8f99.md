---
id: 268
title: ejabberd external component 연동
date: 2015-03-04T21:01:51+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=268
permalink: '/2015/03/04/ejabberd-external-component-%ec%97%b0%eb%8f%99/'
categories:
  - ejabberd
tags:
  - bot
  - component
---
## xmpp component 개요

xmpp에는 external component 관련 공식 프로토콜인 [Jabber Component Protocol](http://xmpp.org/extensions/xep-0114.html)이 있다.

Jabber Component Protocol은 외부 컴포넌트를 XMPP서버와 연동하기 위한 규약을 제안하는데 크게 accept와 connect에 대한 XML 스키마와 handshake flow를 명세하고 있다.

xmpp의 external component는 프로토콜만 준수한다면 어떤 언어를 사용해서도 구현할 수 있다. 3rd-party 컴포넌트를 ejabberd와 연동하는 방법은 [Install Components and Transports to othe networks](https://www.ejabberd.im/tutorials-transports) 문서를 참고하도록 한다.

## echo-bot component 만들어서 연동해 보기

node-xmpp-component 모듈을 사용해서 간단한 에코봇을 컴포넌트로 만들어 ejabberd와 연동해 보도록 하자.

우선 다음과 같이 node-xmpp-component와 node-xmpp-core를 설치한다.

```
  
$ npm install node-xmpp-component
  
$ npm install node-xmpp-core
  
```

다음과 같이 에코 봇을 작성한다.

```javascript
  
var Component = require('node-xmpp-component')
      
, argv = process.argv
      
, ltx = require('node-xmpp-core').ltx

if (argv.length < 6) {
      
console.error('Usage: node echo_bot.js <my-jid> <my-password> ' +
      
'<server> <port>')
      
process.exit(1)
  
}

var component = new Component({
      
jid: argv[2],
      
password: argv[3],
      
host: argv[4],
      
port: Number(argv[5]),
      
reconnect: true
  
})

component.on('online', function() {
      
console.log('Component is online')

component.on('stanza', function(stanza) {
          
console.log('Received stanza: ', stanza)
          
if (stanza.is('message') && stanza.attrs.type === 'chat') {
              
var i = parseInt(stanza.getChildText('body'))
              
var reply = new ltx.Element('message', { to: stanza.attrs.from, from: stanza.attrs.to, type: 'chat' })
              
reply.c('body').t(stanza)
              
component.send(reply)
          
}
      
})
  
})

component.on('offline', function () {
      
console.log('Component is offline')
  
})

component.on('connect', function () {
      
console.log('Component is connected')
  
})

component.on('reconnect', function () {
      
console.log('Component reconnects …')
  
})

component.on('disconnect', function (e) {
      
console.log('Component is disconnected', e)
  
})

component.on('error', function(e) {
      
console.error(e)
      
process.exit(1)
  
})

process.on('exit', function () {
      
component.end()
  
})
  
```

ejabberd.yml을 다음과 같이 수정한다.

```
  
##
    
\## ejabberd_service: Interact with external components (transports, &#8230;)
    
##
    
&#8211;
       
port: 8888
       
module: ejabberd_service
       
access: all
       
shaper_rule: fast
       
ip: "127.0.0.1"
       
hosts:
         
"foo.hello.world":
           
password: "foo"

```

ejabberd를 시작한 후 다음과 같이 컴포넌트를 시작한다.

```
  
$ ejabberdctl start
  
$ node echo_bot.js foo.hello.world foo localhost 8888
  
```

정상적으로 연결이 될 경우 다음과 같은 메세지가 출력된다.

```
  
Component is connected
  
Component is online
  
```

이 상태에서 XMPP 클라이언트로 접속한 후 foo.hello.world에게 메세지를 전송해 보자.
  
문제가 없을 경우 보낸 메세지가 다시 내게로 돌아오게 된다.

## 활용

xmpp component의 활용은 다양하게 활용될 수 있다. 다른 메신저 서비스에 메세지 라우팅을 하거나, 날씨/사전/FAQ등의 봇 서비스에 활용될 수도 있다.

## 참고

  * [Install Components and Transports to Other Networks](https://www.ejabberd.im/tutorials-transports)
  * [Configuring Ejabberd to use an external component (with XEP-0114)](https://www.ejabberd.im/node/5134)
  * [Jabber Component Protocol](http://xmpp.org/extensions/xep-0114.html)
  * [node-xmpp-component](https://github.com/node-xmpp/node-xmpp-component)