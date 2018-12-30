---
author: jeidee
categories:
- node.js
date: '2015-02-26'
guid: https://erlnote.wordpress.com/?p=259
id: 259
permalink: /2015/02/26/node-js-http-get%ec%97%90%ec%84%9c-multi-chunk-%ec%b2%98%eb%a6%ac/
tags:
- http request
title: node.js http get에서 multi-chunk 처리
url: /2015/02/26/node-js-http-getec9790ec849c-multi-chunk-ecb298eba6ac
---

node.js의 http 또는 https 모듈을 사용해 url request를 할 경우 받는 데이터의 크기가 크게 되면 한 번에 데이터가 오지 않고 여러번 나뉘어 오게 된다.

다음과 같은 코드가 있을 경우,

```javascript
    
http.get({host: host, port: port, path: path},
        
function(res) {
          
res.on('data', function(res_data) {
            
callback(res_data);
          
});
        
});
  
```

res_data 데이터 사이즈가 클 경우 &#8216;data&#8217; 이벤트는 여러 차례 도착하게 된다.
  
따라서 위와 같이 처리하면 안되고 &#8216;readable&#8217; 이벤트를 사용해서 여러 chunk 데이터를 모아서 처리해야 한다.

```javascript
  
http.get({host: host, port: port, path: path},
        
function(res) {
          
var body = '';
          
res.setEncoding('utf8');

res.on('readable', function() {
            
var chunk = this.read() || '';

body += chunk;
          
});

res.on('end', function() {
            
callback(body);
          
});

res.on('error', function(e) {
            
console.log('error', e.message);
          
});
        
});
  
```

&#8216;readable&#8217; 이벤트에서 chunk를 모아, &#8216;end&#8217; 이벤트에서 모아진 데이터를 처리하면 된다.

## 참고

  * http://stackoverflow.com/questions/22015673/node-js-http-request-returns-2-chunks-data-bodies