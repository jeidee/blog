---
author: jeidee
categories:
- go
date: '2015-10-13'
guid: https://erlnote.wordpress.com/?p=600
id: 600
permalink: /2015/10/13/go-restful-api-%ea%b5%ac%ed%98%84/
tags:
- http
- rest api
title: go RESTful API 구현
url: /2015/10/13/go-restful-api-eab5aced9884
---

## 예제

```
  
package main

import (
      
"encoding/json"
      
"fmt"
      
"net/http"
  
)

type ResponseResult struct {
      
Result string \`json:"result"\`
      
Message string \`json:"message"\`
  
}

type ApiServer struct {
  
}

func (as *ApiServer) Start() {
      
http.HandleFunc("/", as.index)

addr := fmt.Sprintf(":%v", 8080)
      
fmt.Printf("API Server is listening on %v", addr)

err := http.ListenAndServe(addr, nil)
      
if err != nil {
          
fmt.Printf("%v", err)
      
}
  
}

func (as \*ApiServer) index(w http.ResponseWriter, r \*http.Request) {
      
w.Header().Set("Content-Type", "application/json")
      
json.NewEncoder(w).Encode(ResponseResult{Result: "400", Message: "Bad request"})
  
}

func main() {
      
as := ApiServer{}
      
as.Start()
  
}
  
```

## 참고

  * [Making a RESTful JSON API in Go](http://thenewstack.io/make-a-restful-json-api-go/)