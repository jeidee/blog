---
author: jeidee
categories:
- node.js
date: '2015-01-22'
guid: https://erlnote.wordpress.com/2015/01/22/webstorm%ec%97%90%ec%84%9c-node-js-%ed%94%84%eb%a1%9c%ec%a0%9d%ed%8a%b8-%ec%83%9d%ec%84%b1-%ec%8b%9c-npm-eaccess-%ec%97%90%eb%9f%ac-%eb%b0%9c%ec%83%9d/
id: 41
permalink: /2015/01/22/webstorm%ec%97%90%ec%84%9c-node-js-%ed%94%84%eb%a1%9c%ec%a0%9d%ed%8a%b8-%ec%83%9d%ec%84%b1-%ec%8b%9c-npm-eaccess-%ec%97%90%eb%9f%ac-%eb%b0%9c%ec%83%9d/
tags:
- webstorm
title: webstorm에서 node.js 프로젝트 생성 시 .npm eaccess 에러 발생
url: /2015/01/22/webstormec9790ec849c-node-js-ed9484eba19ceca09ded8ab8-ec839dec84b1-ec8b9c-npm-eaccess-ec9790eb9fac-ebb09cec839d
---

다음과 같이 .npm 디렉토리의 소유권을 변경한다.

```
      
$ sudo chown -R Omar ~/.npm
  
```