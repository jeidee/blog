---
id: 604
title: erlang rebar의 project template을 활용한 application 뼈대 만들기
date: 2015-11-06T11:18:59+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=604
permalink: '/2015/11/06/erlang-rebar%ec%9d%98-project-template%ec%9d%84-%ed%99%9c%ec%9a%a9%ed%95%9c-application-%eb%bc%88%eb%8c%80-%eb%a7%8c%eb%93%a4%ea%b8%b0/'
categories:
  - erlang
tags:
  - rebar
---
## rebar project template

rebar의 project template 기능을 활용해 erlang application 프로젝트를 손쉽게 생성하는 방법을 살펴보자.

[Richard Jones의 포스트](http://www.metabrew.com/article/erlang-rebar-tutorial-generating-releases-upgrades)를 보고 rebar 실습을 따라해 보니, 현재의 erlang(17.5)과 rebar(2.6.1) 버전으로는 원활하게 진행되지 않았다.

구글링 중에 [rebar의 프로젝트 템플릿을 활용하는 방법](https://github.com/robspassky/rebar-project-template)을 찾게 되었는데 몇 부분만 수정하여 문제없이 사용할 수 있었다.

해당 Repository를 Fork해서 오류부분을 수정해 보았다.
  
https://github.com/jeidee/rebar-project-template

사용법은 다음과 같다.

### rebar 설치

우분투라면 다음과 같이 설치한다.

```
  
$ sudo apt-get install rebar
  
```

[rebar repository](https://github.com/rebar/rebar)에서 소스를 내려 받아 빌드해서 사용해도 된다.

또는 다음 링크를 통해 직접 다운로드해서 사용할 수 있다.
  
https://github.com/rebar/rebar/wiki/rebar

### rebar project template 설치

```
  
$ git clone https://github.com/jeidee/rebar-project-template.git ~/.rebar/templates
  
```

### 프로젝트 생성

myapp이라는 프로젝트를 생성한다고 해 보자.

```
  
$ rebar create template=project projectid=myapp
  
$ cd myapp
  
```

### 빌드 및 실행

```
  
$ make && make console
  
```