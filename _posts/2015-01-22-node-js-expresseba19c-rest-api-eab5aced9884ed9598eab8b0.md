---
id: 43
title: node.js express로 rest api 구현하기
date: 2015-01-22T00:07:56+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/22/node-js-express%eb%a1%9c-rest-api-%ea%b5%ac%ed%98%84%ed%95%98%ea%b8%b0/'
permalink: '/2015/01/22/node-js-express%eb%a1%9c-rest-api-%ea%b5%ac%ed%98%84%ed%95%98%ea%b8%b0/'
categories:
  - node.js
tags:
  - express
  - rest api
---
## express의 url 라우팅 처리 방식 살펴보기

express로 프로젝트를 생성 후 app.js를 열어 보자.

소스에서 다음과 같은 내용을 찾는다.

```javascript
       
var routes = require('./routes/index');
       
var users = require('./routes/users');
      
&#8230;
       
app.use('/', routes);
       
app.use('/users', users);
  
```

먼저 24라인은 html view engine과 관계가 있으므로 스킵하고 25라인을 살펴보자.
  
25번 라인이 의미하는 바는 다음과 같다.

  * http://localhost:3000/users 이하 url이 입력되면 users object에서 처리한다.

users object는 9번 라인에서 ./routes/users(.js) 모듈에서 가져왔는데, 해당 모듈의 소스는 다음과 같다.

```javascript
        
var express = require('express');
        
var router = express.Router();

/\* GET users listing. \*/
        
router.get('/', function(req, res) {
          
res.send('respond with a resource');
        
});

module.exports = router;
  
```

app.js의 users object는 users.js에서 export한 router object인데 해당 object는 express.Router이다.
  
클라이언트의 웹브라우저에서 http의 get method로 http://localhost:3000/users/ url을 요청할 경우 router.get(&#8216;/&#8217;, &#8230;) 콜백함수가 호출되는데, 이 함수 안에서는 http response로 res.send(&#8216;&#8230;.&#8217;)를 사용하고 있다.

send함수는 매개변수 문자열을 클라이언트 웹브라우저에 돌려주고, 웹브라우저는 해당 문자열을 화면에 출력한다.

REST와 RESTful, 그리고 REST(ful) API에 대한 명확한 의미는 wiki나 다음 링크를 참고하자.
  
http://blog.remotty.com/blog/2014/01/28/lets-study-rest/

위 링크에서 중요하다고 생각되는 부분을 추리면 다음과 같다.

  * URI는 다음과 같이 사용 
      * 대소문자를 구별하는 것을 알고 가급적이면 소문자만을 사용한다.
      * 공백대신 하이픈(-)이나 밑줄(_)을 사용해 구분한다.
      * 확장자 대신 http header의 Accpet를 사용한다.
      * URI에 CRUD를 명시하지 말고 http method를 사용한다.
  * http method는 다음과 같다. 
      * POST: 등록
      * GET: 조회
      * PUT: 수정, or [PATCH](http://weblog.rubyonrails.org/2012/2/26/edge-rails-patch-is-the-new-primary-http-method-for-updates/)
      * DELETE: 삭제

일단은 이정도만 숙지하고 rest api(본래는 RESTful API이지만 작성의 편의상 이와 같이 사용하도록 함)를 구현해 보자.

## rest api 구현

app.js에 다음과 같이 추가한다.

```javascript
       
var routes = require('./routes/index');
       
var users = require('./routes/users');
       
var apis = require('./routes/apis');
       
&#8230;
       
app.use('/', routes);
       
app.use('/users', users);
       
app.use('/api', apis);
  
```

추가한 라인은 10번과 27번 라인이다.
  
그런 후 users.js를 복사해 apis.js를 하나 생성한다.

```
      
$ cp routes/users.js apis.js
  
```

apis.js를 열어 다음과 같이 수정한다.

```javascript
       
var express = require('express');
       
var debug = require('debug')('my-application');
       
var url = require('url');
       
var router = express.Router();

/\* GET users listing. \*/
       
router.get('/', function(req, res) {
         
var query = url.parse(req.url, true).query;
         
debug('get query = ', query)
         
res.send(query);
       
});

router.get('/session', function(req, res) {
         
var query = url.parse(req.url, true).query;
         
debug('get query = ', query)
         
res.send(query);
       
});

router.get('/session/list', function(req, res) {
         
var query = url.parse(req.url, true).query;
         
debug('get query = ', query)
         
res.send(query);
       
});

module.exports = router;
  
```

꽤 많은 라인을 추가했는데, 우선 테스트해 보기 전에 url 패키지를 설치해야 한다.
  
다음과 같이 package.json을 열어 의존성 패키지 목록에 url 패키지를 추가하자.(16번 라인)

```
       
{
         
"name": "application-name",
         
"version": "0.0.1",
         
"private": true,
         
"scripts": {
           
"start": "./bin/www"
         
},
         
"dependencies": {
           
"express": "~4.0.0",
           
"static-favicon": "~1.0.0",
           
"morgan": "~1.0.0",
           
"cookie-parser": "~1.0.1",
           
"body-parser": "~1.0.0",
           
"debug": "~0.7.4",
           
"jade": "~1.3.0",
           
"url": "*"
         
}
  
```

그런 후 프로젝트의 루트 디렉토리에서 다음과 같이 입력해 새로운 패키지를 설치한다.

```
      
$ sudo npm install
  
```

그런 후 app을 실행한다.

```
      
$ npm start
  
```

웹브라우저에서 다음 url을 각각 입력해 결과를 확인한다.

http://localhost:3000/api
  
http://localhost:3000/api/session
  
http://localhost:3000/api/session?hello=you&world=me
  
http://localhost:3000/api/session/list
  
http://localhost:3000/api/session/list?hello=you&world=me

마직막 링크의 결과가 다음과 같이 출력되면 잘 진행하고 있는 것이다.

```
      
{
          
hello: "you",
          
world: "me"
      
}
  
```

이제 각자의 상황에 맞게 post, put, delete를 구현하면 된다.