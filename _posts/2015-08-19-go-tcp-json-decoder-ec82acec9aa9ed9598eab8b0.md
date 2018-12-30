---
author: jeidee
categories:
- go
date: '2015-08-19'
guid: https://erlnote.wordpress.com/?p=558
id: 558
permalink: /2015/08/19/go-tcp-json-decoder-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/
tags:
- json
- tcp
title: go tcp + json decoder 사용하기
url: /2015/08/19/go-tcp-json-decoder-ec82acec9aa9ed9598eab8b0
---

다음 코드는 클라이언트에서 보낸 json 패킷을 디코딩하는 간단한 tcp 서버 예제코드이다.

```
  
package main

import (
      
"encoding/json"
      
"log"
      
"net"
  
)

func main() {
      
ln, err := net.Listen("tcp", ":9080")
      
if err != nil {
          
log.Fatal(err)
          
return
      
}
      
defer ln.Close()

for {
          
c, err := ln.Accept()
          
if err != nil {
              
log.Fatalln("Can't accept new client!", err)
              
return
          
}
          
defer c.Close()

go requestHandler(c)
      
}
  
}

type Packet struct {
      
Id int16 \`json:"id"\`
      
Data interface{} \`json:"data"\`
  
}

func requestHandler(c net.Conn) {
      
d := json.NewDecoder(c)

var packet Packet

for {
          
err := d.Decode(&packet)
          
if err != nil {
              
log.Println("Invalid json format!\n", err)
              
c.Close()
              
return
          
}

log.Println(packet, err)
      
}
  
}
  
```

json 포맷이 아닌 데이터를 수신하면 해당 클라이언트의 연결을 닫는다.

클라이언트에서는 다음과 같이 완성된 json 문자열을 한 번에 보내거나,
  
각각 여러 조각으로 분리해서 보낼 수 있다.

```
  
{"id":1, "data":{}}
  
{"id":1,
   
"data":{}}
  
```

서버는 d.Decode(&#8230;) 함수에서 클라이언트에서 보낸 데이터를 수신한 후,
  
수신된 데이터를 조합해 완성된 json 데이터를 생성(디코딩)한다.

d.Decode(&packet) 함수에 주어진 packet 데이터 타입으로 json 데이터를 캐스팅할 수 없다면 packet에는 nil을 대입한다.

다음과 같이 map[string]interface{} 타입의 변수를 d.Decode(&#8230;) 함수의 매개변수로 넘겨줄 경우,
  
어떤 포맷의 json 데이터도 디코딩해서 가져올 수 있다.

```
  
var v map[string]interface{}

err := d.Decode(&v)
  
```