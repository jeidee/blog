---
title: "Windows 10에서 HYPER-V 사용해서 CoreOS 설치"
date: 2018-02-13T10:07:39+09:00
author: jeidee
categories:
- docker
tags:
- docker
- coreos
- hyperv
---

다음 글을 참고한다.

* http://zgundam.tistory.com/133

만약 coreos-install 과정에서 WGET 관련 오류가 발생한다면 다음과 같이 진행한다.

```
$ sudo su -

# echo "check_certificate = off" &gt;&gt; ~/.wgetrc

# coreos-install ~~~
```

* https://superuser.com/questions/508696/wget-without-no-check-certificate