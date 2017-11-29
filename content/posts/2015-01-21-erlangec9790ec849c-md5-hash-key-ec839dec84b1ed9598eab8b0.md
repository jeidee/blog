---
author: jeidee
categories:
- erlang
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/erlang%ec%97%90%ec%84%9c-md5-hash-key-%ec%83%9d%ec%84%b1%ed%95%98%ea%b8%b0/
id: 9
permalink: /2015/01/21/erlang%ec%97%90%ec%84%9c-md5-hash-key-%ec%83%9d%ec%84%b1%ed%95%98%ea%b8%b0/
title: erlang에서 md5 hash key 생성하기
url: /2015/01/21/erlangec9790ec849c-md5-hash-key-ec839dec84b1ed9598eab8b0
---

erlang 모듈의 md5/1 함수를 사용하면 md5 hash 를 생성할 수 있다.

```erlang
      
33> erlang:md5("abcdefg").
  
<<122,198,108,15,20,141,233,81,155,139,210,100,49,44,77,
    
100>>
  
```

md5는 128bit 숫자이고 위의 결과가 의미하는 바는 1byte \* 16개의 binary 데이터를 의미하기 때문에 8 \* 16 = 128(bit)가 된다.

1byte 정수를 16진수(hex code) 문자열로 변환해서 출력하고 싶을 경우 바이너리를 해석(binary comprehensions, <=)해서 다음과 같이 출력할 수 있다.

```erlang
      
lists:flatten([io_lib:format("~2.16.0b", [B]) || <<B>> <= erlang:md5("abcd")]).
  
```

리스트 해석(list comprehensions)과 달리 바이너리 해석은 <- 대신 <= 연산자를 사용한다.

> 리스트 해석은 리스트의 요소를 하나씩 추출해 Fun/1에 따라 처리한 후 리스트를 변환한다(결과 리스트를 반환한다).
    
> [F(X) || X <- L] 형식의 구문을 사용한다. 

위 코드에서 바이너리 해석하는 부분은 다음과 같다.

```erlang
      
io_lib:format("~2.16.0b", [B]) || <<B>> <= erlang:md5("abcd")]
  
```