---
author: jeidee
categories:
- go
date: '2015-08-31'
guid: https://erlnote.wordpress.com/?p=574
id: 574
permalink: /2015/08/31/go-console-progress-bar/
tags:
- progress bar
title: go console progress bar
url: /2015/08/31/go-console-progress-bar
---

console에서 진행 상태를 프로그레스바 형태로 출력하고자 할 때 다음 패키지를 사용할 수 있다.

  * https://github.com/cheggaaa/pb

사용법은 다음과 같이 간단하다.

```
  
package main

import (
      
"github.com/cheggaaa/pb"
      
"time"
  
)

func main() {
      
count := 100000
      
bar := pb.StartNew(count)
      
for i := 0; i < count; i++ {
          
bar.Increment()
          
time.Sleep(time.Millisecond)
      
}
      
bar.FinishPrint("The End!")
  
}
  
```

console에서 프로그레스바를 출력하는 기능의 핵심은,
  
고정된 위치에 문자열을 출력하는 것이라 할 수 있다.
  
(보통 Print()문을 사용할 경우 커서가 계속 이동하며 \r\n을 만나면 다음 줄의 처음으로 커서를 이동)

즉, 지정된 위치에 지정된 문자를 계속 출력하고자 한다면 \r(캐리지 리턴) 제어문자를 활용하면 된다.
  
\r은 현재 라인의 첫번째 컬럼으로 커서를 이동시키므로 고정된 위치에 계속해서 다른 문자열을 대치 출력할 수 있게 된다.

```
  
fmt.Print("r=>")
  
fmt.Print("r==>")
  
&#8230;
  
fmt.Print("r========>")
  
```