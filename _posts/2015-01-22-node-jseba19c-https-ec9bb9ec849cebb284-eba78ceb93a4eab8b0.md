---
id: 39
title: node.js로 https 웹서버 만들기
date: 2015-01-22T00:04:59+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/22/node-js%eb%a1%9c-https-%ec%9b%b9%ec%84%9c%eb%b2%84-%eb%a7%8c%eb%93%a4%ea%b8%b0/'
permalink: '/2015/01/22/node-js%eb%a1%9c-https-%ec%9b%b9%ec%84%9c%eb%b2%84-%eb%a7%8c%eb%93%a4%ea%b8%b0/'
categories:
  - node.js
tags:
  - https
---
다음 문서를 참고했습니다.
  
http://blog.saltfactory.net/221

# 준비

openssl이 설치되어 있고 ubuntu에서 작업한다고 가정합니다.
  
설치되어 있지 않을 경우 다음과 같이 설치합니다.

```
      
sudo apt-get install openssl
  
```

개인키를 생성합니다.

```
      
openssl genrsa 1024 > key.pem
  
```

만들어진 개인키를 사용해 cert 인증서 파일을 생성합니다.