---
author: jeidee
categories:
- utility
date: '2015-03-12'
guid: https://erlnote.wordpress.com/?p=279
id: 279
permalink: /2015/03/12/ubunt-open-file-max-%ec%88%98%ec%a0%95%ed%95%98%ea%b8%b0/
tags:
- open file max
- ubuntu
title: ubunt open file max 수정하기
url: /2015/03/12/ubunt-open-file-max-ec8898eca095ed9598eab8b0
---

test 계정이 있다고 가정하고 다음과 같이 작업합니다.

1) sysctl.conf 수정

```
  
$ sudo vi /etc/sysctl.conf
  
fs.file-max = 1513687

&#8230;

$ sudo sysctl -p
  
fs.file-max = 1513687
  
```

2) limits.conf 수정

```
  
$ sudo vi /etc/security/limits.conf
  
test soft nofile 100000
  
test hard nofile 100000
  
```

3) reboot

```
  
$ sudo reboot
  
&#8230;
  
$ ulimit -n
  
100000
  
```

## 참고

  * [file의 open handle 개수 늘리기](http://egloos.zum.com/mcchae/v/11136124)