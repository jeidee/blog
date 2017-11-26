---
id: 133
title: erlang intellij에서 개발하기
date: 2015-02-01T16:51:35+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/02/01/erlang-intellij%ec%97%90%ec%84%9c-%ea%b0%9c%eb%b0%9c%ed%95%98%ea%b8%b0/'
permalink: '/2015/02/01/erlang-intellij%ec%97%90%ec%84%9c-%ea%b0%9c%eb%b0%9c%ed%95%98%ea%b8%b0/'
categories:
  - erlang
tags:
  - intellij
---
현재까지 찾아본 바로는 쓸만한 erlang ide는 eclipse + erlide와 intellij + erlang plugin 정도가 될 것 같다.
  
webstorm의 경우 erlang plugin을 설치하고 소스코드를 편집할 수 있지만, 프로젝트를 생성하거나 컴파일하고 실행하는 등의 작업은 할 수 없기 때문에 제외한다.

eclipse에 비해 intellij가 상대적으로 많은 기능을 제외하고 편집기 자체도 뛰어나며 community edition을 사용할 경우 무료로 사용할 수 있기 때문에 intellij를 사용하기로 한다.

## intellij에 elang plugin 설치

우선 intellij community edition을 설치한다.(JetBrains 홈페이지에서 다운로드할 수 있다.)

intellij를 실행한 후 Files->Settings(맥의 경우 Preference) 메뉴를 열어 Plugins 탭을 찾아 선택한다.

intellij를 처음 실행할 경우 Welcome 팝업에서 하단의 Configure->Plugins를 선택해도 된다.

Plugins 팝업 윈도우의 하다에서 Browse repositories&#8230; 버튼을 클릭한 후,
  
검색 텍스트박스에 Erlang을 입력해서 해당 플러그인 설치한다.

플러그인을 설치한 후 intellij를 재시작한다.

## erlang project 만들기

welcome 팝업에서 create new project를 선택한 후 erlang 프로젝트를 선택하고 next 버튼을 클릭한다.

Project SDK 선택 화면이 출력되는데,
  
erlang 설치 경로를 선택(보통은 자동으로 경로 선택됨)한다.
  
일반적으로 /usr/local/lib/erlang이나 /usr/lib/erlang이 될 것 이다.
  
(osx의 경우 /opt/local/lib/erlang)

프로젝트를 생성한 후 New -> Erlang File을 선택해 Erlang 모듈을 생성한다.
  
주의할 것은 .erl 파일은 src 경로 하위에 위치해야 한다는 것이다.
  
그렇지 않을 경우 컴파일에서 제외된다.

src 경로에 새로운 .erl파일을 추가한 후 컴파일을 하면 out폴더가 생기며 out/production/프로젝트명 하위에 컴파일 결과물이 생성된다.

## 그 외

rebar 또는 debugger와 통합하는 방법은 [intellij erlang plugin 사이트](http://ignatov.github.io/intellij-erlang/)를 참고하자.