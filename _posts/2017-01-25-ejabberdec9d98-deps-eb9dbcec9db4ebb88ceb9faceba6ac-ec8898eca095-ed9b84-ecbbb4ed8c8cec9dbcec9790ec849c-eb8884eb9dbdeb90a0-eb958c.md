---
id: 1810
title: ejabberd의 deps 라이브러리 수정 후 컴파일에서 누락될 때
date: 2017-01-25T13:33:10+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1810
permalink: '/2017/01/25/ejabberd%ec%9d%98-deps-%eb%9d%bc%ec%9d%b4%eb%b8%8c%eb%9f%ac%eb%a6%ac-%ec%88%98%ec%a0%95-%ed%9b%84-%ec%bb%b4%ed%8c%8c%ec%9d%bc%ec%97%90%ec%84%9c-%eb%88%84%eb%9d%bd%eb%90%a0-%eb%95%8c/'
categories:
  - ejabberd
  - erlang
---
Makefile.in을 열어 skip\_deps=true 부분을 찾아 skip\_deps=false로 수정하면 된다.