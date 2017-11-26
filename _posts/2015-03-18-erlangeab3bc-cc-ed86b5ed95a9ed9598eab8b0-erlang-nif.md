---
id: 285
title: 'erlang과 c/c++ 통합하기 &#8211; erlang nif'
date: 2015-03-18T18:07:10+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=285
permalink: '/2015/03/18/erlang%ea%b3%bc-cc-%ed%86%b5%ed%95%a9%ed%95%98%ea%b8%b0-erlang-nif/'
categories:
  - erlang
tags:
  - nif
  - nifpp
---
erlang과 다른 언어를 통합하려면 포트를 사용하면 된다.

포트의 경우erlang 프로세스에서 다른 언어로 작성된 프로세스를 자식 프로세스로 생성하고, 두 프로세스간 입출력 스트림을 파이프로 연결해 통신하는 방법이라고 할 수 있다.

c언어의 경우 포트 이외에 R14부터 채용된 erlang nif을 사용해서 통합할 수 있는데, 포트보다 사용방법이 간단하다. (c++의 경우 wrapper인 [nifpp](https://github.com/goertzenator/nifpp)를 사용해서 구현할 수 있다.)

자세한 내용은 하단의 링크를 참고하도록 한다.

## 참고

  * [Erlang NIFs](http://www.erlang.org/doc/tutorial/nif.html)
  * [How to integrate C with Erlang program example or Erlang NIFs](http://erlycoder.com/59/erlang-how-to-integrate-c-with-erlang-program-example-or-erlang-nifs-native-implemented-functions-us)
  * [A c NIF wrapper for erlang to support aes/ecb/pkcs5padding add star](http://x3ge.com/?p=998)
  * [Erlang과 C의 연결](http://blog.daum.net/funfunction/19)
  * [C++11 Wrapper for Erlang NIF API/nifpp](https://github.com/goertzenator/nifpp)