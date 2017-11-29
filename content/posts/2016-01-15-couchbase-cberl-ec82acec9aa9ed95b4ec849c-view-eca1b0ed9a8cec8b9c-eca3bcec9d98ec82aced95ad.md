---
author: jeidee
categories:
- couchbase
- erlang
date: '2016-01-15'
guid: https://erlnote.wordpress.com/?p=829
id: 829
permalink: /2016/01/15/couchbase-cberl-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-view-%ec%a1%b0%ed%9a%8c%ec%8b%9c-%ec%a3%bc%ec%9d%98%ec%82%ac%ed%95%ad/
tags:
- cberl
title: couchbase cberl 사용해서 view 조회시 주의사항
url: /2016/01/15/couchbase-cberl-ec82acec9aa9ed95b4ec849c-view-eca1b0ed9a8cec8b9c-eca3bcec9d98ec82aced95ad
---

couchbase의 view 조회시 데이터의 최신 본을 얻지 못할 수도 있다.
  
이러한 이유는 속도의 향상을 위해 stale옵션의 기본값이 true로 설정되어 있어서 그런데, 명시적으로 stale옵션을 false로 지정해야 최신 데이터가 적용된 view를 구할 수 있다.

cberl:view()함수를 사용할 때, stale옵션을 false로 지정하는 방법은 다음과 같다.

https://gist.github.com/jeidee/79e08693d3c6be338e31

Args에 {stale, false}를 추가하면 된다.