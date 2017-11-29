---
author: jeidee
categories:
- node.js
date: '2015-01-22'
guid: https://erlnote.wordpress.com/2015/01/22/nodejs-https-%ec%84%a4%ec%a0%95/
id: 40
permalink: /2015/01/22/nodejs-https-%ec%84%a4%ec%a0%95/
tags:
- https
title: nodejs https 설정
url: /2015/01/22/nodejs-https-ec84a4eca095
---

nodejs에 express사용해서 개발하고 있다면 다음과 같이 처리하면 된다.

```javascript
      
var debug = require('debug')('test_app');
      
var app = require('../app');
      
var http = require('http');
      
var https = require('https');
      
var fs = require('fs');

//var port = 90;
      
var port2 = 443;

//http.createServer(app).listen(port, function() {
      
// debug('http server listening on port' + port);
      
//});

var options = {
        
key: fs.readFileSync('key.pem'),
        
cert: fs.readFileSync('cert.pem')
      
};

https.createServer(options, app).listen(port2, function() {
        
debug('https server listening on port' + port2);
      
});
  
```