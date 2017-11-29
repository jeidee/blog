---
author: jeidee
categories:
- erlang
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/erlang-%ec%9d%98-case%ec%99%80-if/
id: 8
permalink: /2015/01/21/erlang-%ec%9d%98-case%ec%99%80-if/
title: erlang 의 case와 if
url: /2015/01/21/erlang-ec9d98-caseec9980-if
---

erlang에서는 조건 분기문에 해당하는 case와 if가 다른 언어와는 형태가 조금 다르다.
  
처음 보면 어떻게 사용해야 할지 감이 오지 않는데, 알고 보면 다른 언어와 크게 다르지도 않다는 것을 알 수 있다.

erlang의 if와 case는 가드(guard)를 이용한 구문으로 되어 있는데, if와 case를 살펴보기 전에 먼저 가드를 간략히 살펴보자.

## guard

가드는 true와 false 애텀으로 평가받는 식이며 다음과 같은 형식으로 사용할 수 있다.

```erlang
      
max(X, Y) when X > Y -> X;
      
max(X, Y) -> Y.
  
```

위 예제는 max(X, Y) 함수를 구현한 코드인데 when X > Y -> X; 부분이 가드 시퀀스에 해당한다.
  
가드 시퀀스는 가드와 가드 평가식의 조합을 의미하며,
  
X > Y가 가드, -> X가 가드 평가식이 된다.

여러 가드가 and로 조합해야 할 때는 쉼표(,)로 구분하며, 가드 시퀀스가 복수개일 경우에는 세미콜론(;)으로 가드 시퀀스를 구분한다.

## if

if식을 살펴 보자.

```erlang
      
if
          
GuardSeq1 ->

Body1;
          
&#8230;;
          
GuardSeqN ->
              
BodyN
      
end
  
```

GuardSeq1부터 순차적으로 평가가 이뤄지며, 가드 평가로 얻어진 값이 true 애텀일 경우 해당 Body 식을 평가한 값이 if의 결과가 된다.

> 평가란 식을 수행해 결과를 얻는 것을 의미한다. 

모든 가드 시퀀스를 평가해도 true가 없을 경우 if 식은 오류를 발생시키므로 다른 언어의 if ~ else 구문처럼 사용하고자 한다면 다음과 같이 작성해야 한다.

```erlang
      
if
          
GuardSeq1 ->
              
Body1;
          
&#8230;;
          
GuardSeqN ->
              
BodyN;
          
true ->
              
BodyElse
      
end
  
```

모든 가드 시퀀스를 평가한 후 마지막 true 애텀이 평가되어 오류 없이 if else 처럼 동작하게 된다.
  
매 가드 시퀀스는 세미콜론(;)으로 구분하며 마지막 평가식의 경우 세미콜론 없이 입력해야 함을 주의하자.

## case

먼저 구문을 살펴보자.

```erlang
      
case Expr of
          
Pattern1 [when GuardSeq1] ->
              
Body1;
          
&#8230;;
          
PatternN [when GuardSeqN] ->
              
BodyN
      
end
  
```

먼저 Expr 식을 평가하고 그 결과를 Pattern1부터 차례로 매치를 진행한다. 패턴 매치를 할 때는 when 가드를 써서 추가 조건을 평가할 수도 있다.
  
매치가 성공할 경우 해당 Body식을 평가하고 그 결과값이 case의 결과가 된다.
  
만약 매치하는 패턴이 없을 경우 if 식과 마찬가지로 오류가 발생한다.
  
따라서 if 식에서 else를 처리하듯이 하려면 마지막 패턴을 _ 애텀으로 매치하면 일치하는 패턴이 없다 하더라도 예외 없이 실행시킬 수 있다.

> _ 애텀은 어떤 값도 매치시킬 수 있는 애텀이다. 

## examples

다음 예제는 Msg 변수를 &#8220;OK&#8221;와 비교하는 구문을 if와 case로 구현한 코드이다.

**if**

```erlang
      
if
          
Msg == "OK" ->
              
io:format("This is OK");
          
true ->
              
io:format("This is not OK")
      
end.
  
```

**case**

```erlang
      
case Msg of
          
"OK" -> io:format("This is OK");
          
_ -> io:format("This is not OK")
      
end.
  
```