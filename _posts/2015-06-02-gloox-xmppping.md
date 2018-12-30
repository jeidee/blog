---
author: jeidee
categories:
- c++
- ejabberd
date: '2015-06-02'
geo_public:
- '0'
guid: https://erlnote.wordpress.com/?p=367
id: 367
permalink: /2015/06/02/gloox-xmppping/
tags:
- gloox
title: gloox xmppPing
url: /2015/06/02/gloox-xmppping
---

gloox의 경우 tcp 연결이 비정상적으로 종료되었을 때 disconnect를 감지할 수 없는 경우가 있다.

이럴 경우를 대비해 xmpp 서버에 주기적으로 ping을 보내고 pong을 수신해서 연결 상태를 관리하는 것이 좋다.

```cpp
      
class MsgClient : public MessageSessionHandler
      
, &#8230;
      
, EventHandler
      
{
      
private:
          
int m_heartBeat;
      
};

void MsgClient::heartBeat() {
          
m\_client->xmppPing(m\_client->jid(), this);
          
if (++m_heartBeat > 3) {
              
LOGD("허트비트 무응답 초과로 연결 종료!");
              
m_client->disconnect();
          
}
      
}

void MsgClient::handleEvent(const Event& event)
      
{
          
switch (event.eventType())
          
{
              
case Event::PingPing:
                  
LOGD("PingPing");
                  
break;
              
case Event::PingPong:
                  
LOGD("PingPong");
                  
&#8211;m_heartBeat;
                  
break;
              
case Event::PingError:
                  
LOGE("PingError");
                  
break;
              
default:
                  
break;
          
}
          
return;
      
}

// ConnectionListener
      
void MsgClient::onConnect() {
          
LOGD("connected!!!");

m_heartBeat = 0;
  
```

pingpong 이벤트를 수신하기 위해서 EventHandler 인터페이스를 구현해야하고 onConnect()가 콜백될 때 m_heartBeat 값을 초기화해야 한다.

## 출처

  * [C++ / Gloox: how to check when connection is down?](http://stackoverflow.com/questions/3543294/c-gloox-how-to-check-when-connection-is-down)