---
title: "Docker Insecure Registry 사용"
date: 2018-04-19T10:07:39+09:00
author: jeidee
permalink: /2018/04/19/docker-insecure-registry-사용
categories:
- docker
tags:
- docker
---

다음 파일을 수정(또는 새로 생성)하고 docker machine을 다시 시작한다.

* Windows : C:\ProgramData\docker\config\daemon.json
* Linux : /etc/docker/daemon.json

```
{
  "insecure-registries" : ["myregistrydomain.com:5000"]
}
```