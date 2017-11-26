---
id: 47
title: node.js express에서 swig view engine 사용하기
date: 2015-01-22T00:12:17+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/22/node-js-express%ec%97%90%ec%84%9c-swig-view-engine-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/'
permalink: '/2015/01/22/node-js-express%ec%97%90%ec%84%9c-swig-view-engine-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/'
categories:
  - node.js
tags:
  - express
  - swig
---
express view engine으로 swig를 사용해 보자.

express로 웹앱의 기본 골격 프로젝트를 생성했다고 가정한다.

package.json에 다음과 같이 dependencies 모듈을 추가해 준다.

```
      
"swig": "1.4.2"
  
```

app.js에 다음 코드를 추가한다.

```javascript
      
var swig = require('swig');
  
```

app.js에서 view engine 관련된 코드를 다음과 같이 수정한다.

```javascript
      
// view engine setup
      
//app.set('views', path.join(__dirname, 'views'));
      
//app.set('view engine', 'jade');
      
app.set('view engine', 'html');
      
app.set('views', __dirname + '/views');
      
app.engine('.html', swig.renderFile);
  
```

cache를 false로 하지 않으면 template html을 수정해도 cache된 template html을 사용하므로 다음과 같이 cache를 false로 설정한다.

```javascript
      
// Swig will cache templates for you, but you can disable
      
// that and use Express's caching instead, if you like:
      
app.set('view cache', false);
      
// To disable Swig's cache, do the following:
      
swig.setDefaults({ cache: false });
  
```

/test url이 get으로 요청되었을 때 test.html 템플릿에 변수를 넘겨 html을 렌더링하는 코드는 다음과 같다.

```javascript
      
app.get('/test', function (req, res) {
          
res.render('test', {
              
name: 'John Doe',
              
fruits: ['Apple', 'Orange', 'Banana']
          
});
      
});
  
```

test.html 템플릿은 다음과 같이 작성해 보자.

```html
      
{&#37; extends 'layout.html' &#37;}

{&#37; block content &#37;}
      
<h4>Hello { { name }}!</h4>
      
<p>Your fruits are below:</p>
      
<ul>
          
{&#37; for fruit in fruits &#37;}
          
<li class="first" endif="" if="" loop.first="">
          
{ { fruit }}
          
</li>
          
{&#37; endfor &#37;}
      
</ul>
      
{&#37; endblock &#37;}
  
```

제대로 출력하기 위해서는 layout.html이 필요한데 다음과 같다.

```html
      
<h3>My Site</h3>
      
{&#37; block content &#37;}
      
Welcome
      
{&#37; endblock &#37;}
  
```

즉, swig는 layout을 지원한다.

## references

  * http://paularmstrong.github.io/swig/
  * http://paularmstrong.github.io/swig/docs/#express
  * http://xgunheex.blogspot.kr/2014/01/var-publicdir.html