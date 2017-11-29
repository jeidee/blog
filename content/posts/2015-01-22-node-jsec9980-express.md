---
author: jeidee
categories:
- node.js
date: '2015-01-22'
guid: https://erlnote.wordpress.com/2015/01/22/node-js%ec%99%80-express/
id: 44
permalink: /2015/01/22/node-js%ec%99%80-express/
tags:
- express
title: node.js와 express
url: /2015/01/22/node-jsec9980-express
---

ubuntu에서 node.js의 express를 사용해 rest api와 webapp을 구현해 본다.

## 개발환경

  * os: ubuntu 14.10
  * node: v0.10.33
  * express: 4.0.0

## express 설치

```
      
$ sudo npm install -g express
      
$ sudo apt-get install node-express-generator
  
```

## express app 생성

```
      
$ express hello
      
$ cd hello
      
$ npm install
  
```

npm install을 실행하면 의존성 패키지가 설치된다.
  
이렇게 하면 기본 express webapp이 생성된 상태인데, 다음과 같이 확인해 볼 수 있다.

```
      
$ npm start
  
```

디버그 메세지를 확인하고 싶다면 npm start 전에 다음과 같이 입력한다.

```
      
$ export DEBUG=my-application
      
$ npm start

> application-name@0.0.1 start /home/gss.dev/server/www/msgapi
      
> nodejs ./bin/www

my-application Express server listening on port 3000 +0ms
  
```

브라우저에서 http://localhost:3000을 입력해서 다음과 같은 내용이 출력되면 성공이다.

```
      
Express

Welcome to Express
  
```