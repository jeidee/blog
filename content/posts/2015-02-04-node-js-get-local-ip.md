---
author: jeidee
categories:
- node.js
date: '2015-02-04'
guid: https://erlnote.wordpress.com/2015/02/04/node-js-get-local-ip/
id: 136
permalink: /2015/02/04/node-js-get-local-ip/
title: node.js get local ip
url: /2015/02/04/node-js-get-local-ip
---

node.js에서 지정된 도메인의 IP주소를 찾는 방법은 다음과 같다.

```javascript
      
var getLocalIp = function(callback) {
        
require('dns').lookup(require('os').hostname(), function (err, add, fam) {
          
console.log('addr: '+add);
          
callback(add);
        
});
      
};
  
```

위 코드에서 require(&#8216;os&#8217;).hostname()은 로컬호스트명이며, 해당 값에 다른 도메인을 입력해서 IP주소를 구할 수 있다.