---
author: jeidee
categories:
- angularjs
date: '2016-02-03'
guid: https://erlnote.wordpress.com/?p=918
id: 918
permalink: /2016/02/03/angular-js%ec%99%80-ui-router%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-%ec%9d%b8%ec%a6%9d%ec%9d%b4-%ed%95%84%ec%9a%94%ed%95%9c-%ec%9b%b9%ec%82%ac%ec%9d%b4%ed%8a%b8-%eb%bc%88%eb%8c%80-%eb%a7%8c/
tags:
- authentication
- ui-router
title: Angular JS와 ui-router를 사용해서 인증이 필요한 웹사이트 뼈대 만들기
url: /2016/02/03/angular-jsec9980-ui-routereba5bc-ec82acec9aa9ed95b4ec849c-ec9db8eca69dec9db4-ed9584ec9a94ed959c-ec9bb9ec82acec9db4ed8ab8-ebbc88eb8c80-eba78c
---

백엔드 서버는 cowboy를 사용하고, 프론트엔드는 Angular JS를 사용해서 웹사이트를 구성해 보자.

## 준비

  * AngularJS는 1.4.9 버전을 기준으로 한다.

## 디렉토리 구성

디렉토리는 다음과 같이 구성한다.

```
  
&#8211; root
    
ㄴ myapp
      
|- assets
      
| |- css
      
| |- img
      
| ㄴ js
      
|- src
      
| |- app
      
| | |- models
      
| | |- services
      
| | ㄴ app.js
      
| |- auth
      
| | |- controllers
      
| | |- tmpl
      
| | ㄴ Auth.js
      
&#8230;
      
| ㄴ MyApp.js
      
|- vendor
      
ㄴ index.html

```

위의 구조는 [AngularJS in Action](https://www.manning.com/books/angularjs-in-action) 책을 참고했다.

## AngularJS ui-router 사용하기

ngRouter는 단일 뷰만 사용할 수 있기 때문에 [ui-router](https://github.com/angular-ui/ui-router)를 사용하도록 한다.

index.html파일은 다음과 같은 구조를 갖는다.

https://gist.github.com/jeidee/768db939ec1b46ee20e6

profile view와 contents view를 갖는데 $state에 따라 다음과 같이 처리된다.

https://gist.github.com/jeidee/263c3ba9a92d74108e23

이런 방법 이외에 abstract와 중첩 뷰를 사용해서 처리할 수도 있는 것 같은데 추후 리서치 후 보강하도록 하겠다.

위와 같은 구조는 모든 상태마다 두 개의 뷰(contents와 profile)에 맞는 template과 controller를 지정하는 방식이며, signin 상태를 보면 인증이 처리되기 전에는 profile 뷰에 지정된 template이 없으므로 해당 뷰는 빈 상태로 출력하게 된다.

## ui-router를 사용해서 인증 처리하기

MyApp.js의 마지막 myApp.run(&#8230;) 부분을 보면, 매 상태가 변경될 때마다 상태의 authenticate 속성이 true일 경우(인증이 필요함) 인증처리를 하고 있는 것을 볼 수 있다.

인증 확인을 통과하지 않으면 signin상태로 전이하도록 하고 있는 부분($state.transitionTo(&#8216;signin&#8217;))을 보자.

### AuthService.isAuthenticate()

https://gist.github.com/jeidee/c2b9b638e925774d0749

isAuthenticate() 함수는 백엔드 서버의 REST API를 호출하고 있다. 로그인이 완료된 경우 인증 정보는 쿠키에 저장되어 있다고 가정한다.

## 결론

많이 부족하고 허술하지만 개념을 잡는데 다소나마 도움이 될 만한 내용을 정리해 보았다.
  
이 글을 읽기전에 다음과 같은 기초적인 내용들은 미리 학습할 필요가 있다.

  * HTML5 + CSS + JavaScript Basic
  * AngularJS Basic