---
author: jeidee
categories:
- elixir
date: '2015-06-21'
guid: https://erlnote.wordpress.com/?p=532
id: 532
permalink: /2015/06/21/elixir-where-to-go-next-2121/
tags:
- tutorial
title: Elixir Where to go next (#21/21)
url: /2015/06/21/elixir-where-to-go-next-2121
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

더 배우고 싶은가? 그럼 계속 읽어보자.

## Build your first Elixir project

우리의 첫 프로젝트를 시작하기 위해, elixir는 Mix라는 빌드 툴을 제공한다. 우리는 다음과 같이 간단하게 첫 프로젝트를 만들 수 있다.

```
  
mix new path/to/new/project
  
```

위의 과정을 통해 우리는 슈퍼비전 트리, 설정, 테스트등을 갖는 elixir 응용프로그램을 만들 수 있다. 다음 가이드를 통해 우리는 분산된 노드에 분산된 key-value 저장소를 갖는 응용프로그램을 만드는 방법을 배울 수 있다.

  * [Mix and OTP](http://elixir-lang.org/getting-started/mix-otp/introduction-to-mix.html)

## Meta-programming

elixir는 메타 프로그래밍(코드를 생성하는 코드)을 지원하는 매우 확장성 있는 프로그래밍 언어이다. elixir는 매크로를 통해 메타-프로그래밍을 지원하며 여러 상황에서(특히 DSLs을 작성할 때) 매우 유용하게 사용할 수 있다. 우리는 다음 가이드를 통해 매크로를 사용한 기본 매커니즘을 설명하며 어떻게 매크로를 작성하고 그것들을 DSLs를 만들때 사용하는지 배울 수 있다.

  * [Meta-programming in Elixir](http://elixir-lang.org/getting-started/meta/quote-and-unquote.html)

## Community and othe resources

이 외에도 elixir와 관련된 책과, 스크린캐스트 및 다른 여러 리소스를 찾을 수 있다. 또한 컨퍼런스, 오픈소스 프로젝트, 커뮤니티에서 생성한 학습 자료 등도 꽤 많다.

> 역주 : 안타깝게도 현 시점(2015년 6월)에서 한글 자료는 전무하다. 

만약 elixir와 관련한 어려움이 있다면 irc.freenode.net의 #elixir-lang 채널을 방문하거나 [메일링 리스트](http://groups.google.com/group/elixir-lang-talk)에 메세지를 보낼 수 있음을 기억하도록 한다. 그 곳에서 도움을 받을 수도 있고, [공식 블로그](http://elixir-lang.org/blog/)나 [공식 그룹](http://groups.google.com/group/elixir-lang-core)에서 최신 뉴스와 공지를 받아볼 수도 있다.

최신의 [elixir 소스코드](https://github.com/elixir-lang/elixir)를 살펴볼 수 있다는 것과 [elixir의 공식문서](http://elixir-lang.org/docs.html)를 살펴볼 수 있다는 것을 잊지말자.

## A byte of Erlang

elixir는 Erlang VM에서 동작하기 때문에, 이르거나 뒤늦은 차이는 있겠지만, elixir 개발자는 erlang 라이브러리와 인터페이스할 때가 오게된다. 아래에 Erlang과 관련된 좀 더 전문적인 내용을 포함하는 온라인 리소스의 링크가 있다.

  * [Erlang 구문 : 집중 훈련](http://elixir-lang.org/crash-course.html)에서는 Erlang 구문의 간결한 소개를 제공한다. 각 코드 조각들은 elixir와 동등한 코드로 대응할 수 있다. 이 곳에서 Erlang의 기본 구문을 배울 수 있을 뿐만 아니라, 지금까지 배운 elixir의 내용을 리뷰할 수 있는 기회도 얻을 수 있다.

  * Erlang의 공식 웹 사이트에는 동시성(concurrent) 프로그래밍을 위한 Erlang의 기본 구문을 간략히 살펴볼 수 있는 [튜토리얼](http://www.erlang.org/course/concurrent_programming.html)을 제공한다.

  * [Learn You Some Erlang for Great Good!](http://learnyousomeerlang.com/)사이트에서 Erlang의 뛰어난 소개를 볼 수 있다. 이 곳에서 Erlang의 설계 원리, 표준 라이브러리, 실제 사용례등의 많은 내용을 접해 볼 수 있다. 위에서 언급한 집중 코스를 한 번 읽어 봤다면, 이 책의 처음 몇 챕터는 쉽게 건너 뛸 수 있을 것이다. [동시성을 위한 히치하이커를 위한 안내](http://learnyousomeerlang.com/the-hitchhikers-guide-to-concurrency) 챕터에 다다랐을때 비로소, 진짜 재밌는 시작을 할 수 있게 될 것이다.

## 참고

  * [Elixir Where to go next](http://elixir-lang.org/getting-started/where-to-go-next.html)