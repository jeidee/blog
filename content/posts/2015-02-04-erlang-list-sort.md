---
author: jeidee
categories:
- erlang
date: '2015-02-04'
guid: https://erlnote.wordpress.com/2015/02/04/erlang-list-sort/
id: 137
permalink: /2015/02/04/erlang-list-sort/
tags:
- sort
title: erlang list sort
url: /2015/02/04/erlang-list-sort
---

다음과 같은 리스트가 있다고 가정하자.

```erlang
      
> List = [{a, 10}, {b, 5}, {c, 3}, {d, 6}].
  
```

두번째 요소인 정수값을 기준으로 오름차순으로 정렬하고자 한다면 다음과 같이 한다.

```erlang
      
> lists:sort(fun(A, B) ->
          
{Name1, Order1} = A,
          
{Name2, Order2} = B,
          
Order1 < Order2
          
end, List).
  
```