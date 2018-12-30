---
author: jeidee
categories:
- node.js
date: '2015-01-22'
guid: https://erlnote.wordpress.com/2015/01/22/jade-include/
id: 45
permalink: /2015/01/22/jade-include/
tags:
- jade
title: jade include
url: /2015/01/22/jade-include
---

jade에서 jade, html, css, js 등의 외부 파일을 jade파일 안에 포함시키고 싶을 경우 다음과 같이 include를 사용하면 된다.

```
      
include test.html
  
```

문제는 test.html을 변수명으로 입력 받을 수 있는 방법을 찾을 수가 없어서 다음과 같이 처리할 수 밖에 없었다.(추후에 방법을 찾을 경우 내용 갱신 필요)

```
      
extends layout
      
block content
          
case menu
              
when 'menu1'
                  
include menu1.html
              
when 'menu2'
                  
include menu2.html
              
default
                  
include default.html
  
```

jade는 탭을 사용하지 않으므로 들여쓰기는 공백 2문자로 처리하는 것이 좋다.