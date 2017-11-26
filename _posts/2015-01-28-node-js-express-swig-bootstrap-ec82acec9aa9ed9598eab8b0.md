---
id: 126
title: node.js + express + swig + bootstrap 사용하기
date: 2015-01-28T21:03:47+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/28/node-js-express-swig-bootstrap-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/'
permalink: '/2015/01/28/node-js-express-swig-bootstrap-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/'
categories:
  - node.js
tags:
  - bootstrap
  - express
  - swig
---
## booststrap 설치

bower를 사용해서 설치하기로 한다.
  
그 외의 설치방법은 [한글 booststrap](http://bootstrapk.com/BS3/getting-started) 사이트를 참고하자.

먼저 node.js와 express로 app 프로젝트가 생성되었다고 가정한다.
  
생성된 app 프로젝트의 root에서 다음과 같이 실행한다.

```
      
$ cd public
      
$ bower install bootstrap
  
```

위와 같이 bootstrap을 설치하면 public/bower_components 하위에 bootstrap과 jquery가 설치된다.

## express + swig에 bootstrap 적용

views/layout.html파일을 다음과 같이 만든다.

```html
      
<!DOCTYPE html>
      
<html>
      
<head>
          
<title>UP Messenger</title>
          
<meta name="viewport" content="width=device-width, initial-scale=1.0">
          
<!&#8211; 부트스트랩 &#8211;>
          
<link href="/bower_components/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" media="screen">
          
<!&#8211; jQuery (부트스트랩의 자바스크립트 플러그인을 위해 필요한) &#8211;>
          
<script src="/bower_components/jquery/dist/jquery.js"></script>
          
<!&#8211; 모든 합쳐진 플러그인을 포함하거나 (아래) 필요한 각각의 파일들을 포함하세요 &#8211;>
          
<script src="/bower_components/bootstrap/dist/js/bootstrap.min.js"></script>

<!&#8211; Respond.js 으로 IE8 에서 반응형 기능을 활성화하세요 (https://github.com/scottjehl/Respond) &#8211;>
          
<script src="/bower_components/bootstrap/dist/js/respond.js"></script>
          
<style>
              
/\* Move down content because we have a fixed navbar that is 50px tall \*/
              
body {
                  
padding-top: 50px;
                  
padding-bottom: 20px;
              
}
          
</style>
      
</head>
      
<body>
      
<div class="container">

{&#37; block content &#37;}
          
{&#37; endblock &#37;}

<hr>

<footer>
              
<p>&copy; Company, 2015</p>
          
</footer>
      
</div> <!&#8211; /container &#8211;>

</body>
      
</html>
  
```

views/index.html을 다음과 같이 만든다.

```html
{&#37; extends 'layout.html' &#37;}

{&#37; block content &#37;}
      
<h4>{ { title }}!</h4>

{&#37; endblock &#37;}
```

routes/index.js를 다음과 같이 수정한다.

```javascript
      
/\* GET home page. \*/
      
router.get('/', function(req, res, next) {
        
res.render('index', { title: 'Express' });
      
});
  
```

app.js이하 swig 관련 설정은 [EXPRESS에서 swig view engine 사용하기](https://erlnote.wordpress.com/2015/01/22/node-js-express%EC%97%90%EC%84%9C-swig-view-engine-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/)를 보고 수정하도록 한다.

## Reference

  * [w2schoool bootstrap](http://www.w3schools.com/bootstrap/)
  * [EXPRESS에서 swig view engine 사용하기](https://erlnote.wordpress.com/2015/01/22/node-js-express%EC%97%90%EC%84%9C-swig-view-engine-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/)