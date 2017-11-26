---
id: 16
title: erlang key-value 튜플 리스트에서 key로 value 찾기
date: 2015-01-21T23:32:41+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/erlang-key-value-%ed%8a%9c%ed%94%8c-%eb%a6%ac%ec%8a%a4%ed%8a%b8%ec%97%90%ec%84%9c-key%eb%a1%9c-value-%ec%b0%be%ea%b8%b0/'
permalink: '/2015/01/21/erlang-key-value-%ed%8a%9c%ed%94%8c-%eb%a6%ac%ec%8a%a4%ed%8a%b8%ec%97%90%ec%84%9c-key%eb%a1%9c-value-%ec%b0%be%ea%b8%b0/'
categories:
  - erlang
---
proplists 모듈의 get_value/2를 사용하면 된다.

```erlang
      
proplists:get_value(Key, List).
  
```

예를 들어 다음과 같이 과일의 수량정보를 갖는 리스트가 있다고 하자.

```erlang
      
List = [{apple, 10}, {orange, 20}].
  
```

위 리스트에서 apple을 key로 사용해서 apple의 개수를 얻으려면 다음과 같이 하면 된다.

```erlang
      
proplists:get_value(apple, List).
  
```

튜플 자체를 구하고자 한다면 다음과 같이 한다.

```erlang
      
proplists:lookup(apple, List).
  
```

get\_value/2와 lookup/2은 매치하는 튜플 중에서 첫 번째 엔트리만 반환하기 때문에 모두 찾고자 한다면 get\_all\_values/2나 lookup\_all/2을 사용해야 한다. 단 결과는 리스트로 반환된다.