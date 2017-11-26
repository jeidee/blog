---
id: 572
title: go containter/list를 사용한 Concurrent Queue
date: 2015-08-26T20:04:16+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=572
permalink: '/2015/08/26/go-containterlist%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%9c-concurrent-queue/'
categories:
  - go
tags:
  - queue
---
  * 참고자료 
      * [Stack and Queue in golang](https://gist.github.com/moraes/2141121)

container/list와 sync.Mutex를 사용한 쓰레드(고루틴)에 안전한 Queue는 다음과 같이 만들 수 있다.

```
  
package queue

import (
      
"container/list"
      
"sync"
  
)

type Queue struct {
      
l *list.List
      
lock sync.Mutex
  
}

func NewQueue() *Queue {
      
return &Queue{list.New(), sync.Mutex{}}
  
}

func (q *Queue) Push(i interface{}) {
      
q.lock.Lock()
      
defer q.lock.Unlock()

q.l.PushBack(i)
  
}

func (q *Queue) Pop() (interface{}, bool) {
      
q.lock.Lock()
      
defer q.lock.Unlock()

if q.l.Len() == 0 {
          
return nil, false
      
}

v := q.l.Front()
      
q.l.Remove(v)

return v.Value, true
  
}

func (q *Queue) Len() int {
      
q.lock.Lock()
      
defer q.lock.Unlock()

return q.l.Len()
  
}
  
```