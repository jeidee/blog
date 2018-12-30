---
author: jeidee
categories:
- go
date: '2015-08-31'
guid: https://erlnote.wordpress.com/?p=577
id: 577
permalink: /2015/08/31/go-buffered-channle%ec%9d%84-%ec%9d%b4%ec%9a%a9%ed%95%9c-concurrent-queue-%ea%b5%ac%ed%98%84/
tags:
- concurrent
- queue
title: go buffered channel을 이용한 Concurrent Queue 구현
url: /2015/08/31/go-buffered-channleec9d84-ec9db4ec9aa9ed959c-concurrent-queue-eab5aced9884
---

이전 [포스트](https://erlnote.wordpress.com/2015/08/26/go-containterlist%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-concurrent-queue/)에서는 cotainer/list와 sync/mutex를 사용해서 Concurrent Queue를 구현해 보았다.

해당 큐의 단점은 Multi-Goroutine 동기화를 위해 Mutex를 사용하면서 비용이 발생하고,
  
Pop()함수를 사용할 경우 값이 없을 경우에도 즉시 리턴을 하기 때문에,
  
외부 모듈에서는 큐에서 값을 가져오기 위해 계속해서 Pop()함수를 호출해야 하는 비용이 발생했다.

이러한 단점을 보완하기 위해 go에서 제공하는 buffered channel을 사용해 다음과 같이 구현할 수 있다.

```
  
package queue

import (
      
"sync/atomic"
  
)

type ChanQueue struct {
      
syncChan chan interface{}
      
size int32
  
}

func NewChanQueue(maxSize int) *ChanQueue {
      
return &ChanQueue{syncChan: make(chan interface{}, maxSize)}
  
}

func (q *ChanQueue) Push(v interface{}) {
      
atomic.AddInt32(&q.size, 1)
      
q.syncChan <- v
  
}

func (q *ChanQueue) Pop() (interface{}, bool) {
      
atomic.AddInt32(&q.size, -1)
      
v := <-q.syncChan
      
return v, true
  
}

func (q *ChanQueue) Len() int {
      
return int(q.size)
  
}
  
```

큐 사이즈를 정확하게 유지하기 위해 sync/atomic의 AddInt32() 함수를 사용했다.