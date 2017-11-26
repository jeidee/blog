---
id: 41
title: webstorm에서 node.js 프로젝트 생성 시 .npm eaccess 에러 발생
date: 2015-01-22T00:06:50+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/22/webstorm%ec%97%90%ec%84%9c-node-js-%ed%94%84%eb%a1%9c%ec%a0%9d%ed%8a%b8-%ec%83%9d%ec%84%b1-%ec%8b%9c-npm-eaccess-%ec%97%90%eb%9f%ac-%eb%b0%9c%ec%83%9d/'
permalink: '/2015/01/22/webstorm%ec%97%90%ec%84%9c-node-js-%ed%94%84%eb%a1%9c%ec%a0%9d%ed%8a%b8-%ec%83%9d%ec%84%b1-%ec%8b%9c-npm-eaccess-%ec%97%90%eb%9f%ac-%eb%b0%9c%ec%83%9d/'
categories:
  - node.js
tags:
  - webstorm
---
다음과 같이 .npm 디렉토리의 소유권을 변경한다.

```
      
$ sudo chown -R Omar ~/.npm
  
```