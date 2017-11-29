---
author: jeidee
categories:
- go
date: '2015-08-25'
guid: https://erlnote.wordpress.com/?p=570
id: 570
permalink: /2015/08/25/go-json-%eb%94%94%ec%bd%94%eb%8d%94%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-tcp-%ec%8a%a4%ed%8a%b8%eb%a6%bc%ec%9c%bc%eb%a1%9c-%ec%88%98%ec%8b%a0%ed%95%9c-json-object-%ec%b2%98%eb%a6%ac%ed%95%98/
tags:
- json
- json serialization
title: go json 디코더를 사용해서 TCP 스트림으로 수신한 json object 처리하기
url: /2015/08/25/go-json-eb9494ecbd94eb8d94eba5bc-ec82acec9aa9ed95b4ec849c-tcp-ec8aa4ed8ab8eba6bcec9cbceba19c-ec8898ec8ba0ed959c-json-object-ecb298eba6aced9598
---

우선 net.Conn 인스턴스(c.conn은 net.Conn 인스턴스)를 사용해서 json 디코더를 생성한다.

```
  
d := json.NewDecoder(c.conn)
  
```

수신받은 데이터를 json 오브젝트로 디코딩한다.

```
  
var packet map[string]interface{}
  
err := d.Decode(&packet)
  
```

json 오브젝트를 특정 구조체 데이터로 캐스팅한다.
  
그 전에 송수신 하는 json 오브젝트에 id필드가 있다고 가정하며,
  
id필드 값에 따라 다른 구조체로 캐스팅하도록 한다.

```
  
type ReqLoginPacket struct {
    
Id int \`json:"id"\`
    
UserId string \`json:"user_id"\`
  
}

pid := int(p["id"].(float64))

switch pid {
    
case 1:
      
reqPacket := new(ReqLoginPacket)
      
MapToObject(packet, reqPacket)
    
&#8230;
  
}
  
```

MapToObject/2 함수는 다음과 같다.

```
  
func MapToObject(m map[string]interface{}, o interface{}) error {
    
v, err := json.Marshal(m)
    
if err != nil {
      
return err
    
}

err = json.Unmarshal(v, &o)
    
if err != nil {
      
return err
    
}

return nil
  
}
  
```

위 코드의 문제점은 Marshal/Unmarshal 오버헤드가 크다는 점이다.
  
네트워크 스트림을 json으로 디코딩해 map 오브젝트로 만들고, 이 오브젝트를 다시 byte array로 마샬링한 후 특정 구조체 오브젝트로 언마샬하는 과정이 필요하기 때문에,
  
성능상 큰 손실이 발생할 수 밖에 없다.