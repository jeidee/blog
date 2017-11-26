---
id: 155
title: javascript JSON 문자열을 예쁘게 포맷팅하기
date: 2015-02-10T23:33:21+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/02/10/javascript-json-%eb%ac%b8%ec%9e%90%ec%97%b4%ec%9d%84-%ec%98%88%ec%81%98%ea%b2%8c-%ed%8f%ac%eb%a7%b7%ed%8c%85%ed%95%98%ea%b8%b0/'
permalink: '/2015/02/10/javascript-json-%eb%ac%b8%ec%9e%90%ec%97%b4%ec%9d%84-%ec%98%88%ec%81%98%ea%b2%8c-%ed%8f%ac%eb%a7%b7%ed%8c%85%ed%95%98%ea%b8%b0/'
categories:
  - node.js
tags:
  - javascript
---
다음과 같이 사용할 수 있다.

```javascript
      
var obj = JSON.parse(jsonTxt);
      
var html = '<pre>' + JSON.stringify(obj, null, 4) + '</pre>';
  
```

참고 URL은 다음과 같다.

  * http://stackoverflow.com/questions/8007763/json-formatter-lib