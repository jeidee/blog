---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-%eb%b0%b1%ec%97%85%ea%b3%bc-%eb%b3%b5%ec%9b%90/
id: 24
permalink: /2015/01/21/ejabberd-%eb%b0%b1%ec%97%85%ea%b3%bc-%eb%b3%b5%ec%9b%90/
tags:
- 백업
- 복원
title: ejabberd 백업과 복원
url: /2015/01/21/ejabberd-ebb0b1ec9785eab3bc-ebb3b5ec9b90
---

ejabberdctl의 admin 명령어를 사용해 백업과 복원을 수행할 수 있다.

**백업**

```
      
$ ejabberdctl backup 백업파일명
  
```

**복원**

```
      
$ ejabberdctl restore 백업파일명
  
```