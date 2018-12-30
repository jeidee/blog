---
author: jeidee
categories:
- go
date: '2015-09-03'
guid: https://erlnote.wordpress.com/?p=587
id: 587
permalink: /2015/09/03/go-http-%ec%9a%94%ec%b2%ad-%ec%b2%98%eb%a6%ac/
tags:
- http
title: go http 요청 처리
url: /2015/09/03/go-http-ec9a94ecb2ad-ecb298eba6ac
---

net/http 패키지를 사용하면 된다.

REST API를 사용하는 간단한 예제는 다음과 같다.

```
  
package main

import (
      
"net/http"
      
"fmt"
      
"io/ioutil"
      
"encoding/json"
  
)

func main() {
      
r, err := http.Get("https://example.com/api/&#8230;")
      
if err != nil {
          
fmt.Println(err)
          
return
      
}
      
defer r.Body.Close()

var body []byte
      
body, err = ioutil.ReadAll(r.Body)
      
if err != nil {
          
fmt.Println(err)
          
return
      
}

var m map[string]interface{}
      
json.Unmarshal(body, &m)

fmt.Println("body is ", m)

}
  
```

Response가 JSON 문자열일 경우 json.Unmarshal() 함수를 사용해 map[string]interface{} 타입으로 변환할 수 있다.