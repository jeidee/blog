---
id: 582
title: go coroutines 패턴 구현하기
date: 2015-09-01T09:15:07+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=582
permalink: '/2015/09/01/go-coroutines-%ed%8c%a8%ed%84%b4-%ea%b5%ac%ed%98%84%ed%95%98%ea%b8%b0/'
categories:
  - go
tags:
  - coroutine
---
coroutine은 쓰레드와 유사하다고 볼 수 있다.

다른점은, 쓰레드가 동시에 여러쓰레드에서 병렬적으로 실행된다면, coroutine은 병행성은 갖지만, 특정 시점에 단 하나의 coroutine만 실행됨을 보증한다는 것이다.

goroutine과 coroutine의 차이를 다음과 같이 구분할 수 있다.

  * goroutine은 병렬적이지만 coroutine은 그렇지 않다.
  * goroutine간에는 채널을 통해 통신하며, coroutine간에는 yield와 resume 동작에 의해 통신한다.

c#에서는 yield 키워드를 통해서 iterator를 구현할 때 주로 사용하며, 유니티의 coroutine은 이러한 yield키워드를 사용해서 메인쓰레드에서 여러 함수를 동시에 실행시키는데 사용한다.

### csharp

```csharp
  
public class PowersOf2
  
{
      
static void Main()
      
{
          
// Display powers of 2 up to the exponent of 8:
          
foreach (int i in Power(2, 8))
          
{
              
Console.Write("{0} ", i);
          
}
      
}

public static System.Collections.IEnumerable Power(int number, int exponent)
      
{
          
int result = 1;

for (int i = 0; i < exponent; i++)
          
{
              
result = result * number;
              
yield return result;
          
}
      
}

// Output: 2 4 8 16 32 64 128 256
  
}
  
```

Power/2함수는 주어진 수의 멱수를 구하는 함수인데 열거가능한 집합처럼 취급되었다.
  
일반적으로 함수를 호출하면 함수가 완료되기 전까지 호출한 지점으로 제어권이 넘어오지 않는다.
  
하지만 위의 예제에서는 Power()함수 안의 for(&#8230;)문에서 i가 하나 증가할 때마다 yield return result를 통해 매번 그 결과를 Main()함수의 foreach(&#8230;)문의 i에 전달하고 실행지점이 Power()함수에서 Main()함수로 변경되게 된다.

즉, 싱글쓰레드에서 실행지점만 변경하면서 동시에 두 함수가 동작하는 결과를 낳는다.

### go

go언어에는 이와 동일한 coroutine은 제공하지 않지만, goroutine과 channel을 사용해 동일한 기능을 제공하는 패턴을 구현할 수 있다.

```
  
package main

import (
      
"fmt"
  
)

func main() {
      
number := 2
      
exponent := 8

resume := power(number, exponent)
      
for i := 0; i < exponent; i++ {
          
result := <-resume
          
fmt.Println("Result : ", result)
      
}
  
}

func power(number int, exponent int) chan int {
      
yield := make(chan int)
      
result := 1
      
go func() {
          
for i := 0; i < exponent; i++ {
              
result = result * number
              
yield <- result
          
}
      
}()
      
return yield
  
}
  
```

goroutine은 go언어에서 사용하는 경량프로세스로서, 쓰레드와 유사하다.
  
멀티쓰레드환경에서 발생할 수 있는 많은 문제(Race-condition, Dead-Lock, 절차의 동기화)가 goroutine에서도 동일하게 발생한다.

서로다른 goroutine 문맥에서 절차의 동기화를 위해 channel을 사용할 수 있는데,
  
이러한 channel 동기화를 통해 coroutine 패턴을 구현할 수 있다.

## 참고

  * [Go Coroutines](http://www.golangpatterns.info/concurrency/coroutines)
  * [Concurrent와 Parallel의 차이](http://egloos.zum.com/minjang/v/2517211)