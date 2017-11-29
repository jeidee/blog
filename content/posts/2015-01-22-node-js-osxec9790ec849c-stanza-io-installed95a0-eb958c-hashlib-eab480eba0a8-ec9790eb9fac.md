---
author: jeidee
categories:
- node.js
date: '2015-01-22'
guid: https://erlnote.wordpress.com/2015/01/22/node-js-osx%ec%97%90%ec%84%9c-stanza-io-install%ed%95%a0-%eb%95%8c-hashlib-%ea%b4%80%eb%a0%a8-%ec%97%90%eb%9f%ac/
id: 42
permalink: /2015/01/22/node-js-osx%ec%97%90%ec%84%9c-stanza-io-install%ed%95%a0-%eb%95%8c-hashlib-%ea%b4%80%eb%a0%a8-%ec%97%90%eb%9f%ac/
tags:
- stanza.io
title: node.js osx에서 stanza.io install할 때 hashlib 관련 에러
url: /2015/01/22/node-js-osxec9790ec849c-stanza-io-installed95a0-eb958c-hashlib-eab480eba0a8-ec9790eb9fac
---

다음과 같이 openssl을 upgrade하고 링크를 다시 생성한다.

```
      
brew install &#8211;upgrade openssl
      
brew unlink openssl && brew link openssl &#8211;force
  
```

에러가 발생하는 이유는 macport와 brew의 install 경로가 다음과 같이 다르기 때문이다.

  * macport : /opt/local
  * brew : /usr/local