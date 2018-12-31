---
title: "Docker Registry"
date: 2017-11-30T10:07:39+09:00
author: jeidee
categories:
- docker
tags:
- docker
---

도커 레지스트리를 만들 때 아래 링크의 문서를 참고합니다:

* [나만의 private docker registry 구성하기](https://novemberde.github.io/2017/04/09/Docker_Registry_0.html)

윈도우즈나 Mac이 아니라 리눅스의 도커에서 private docker registry에 push할 때는,
/etc/docker에 daemon.json을 생성하고 다음 내용을 입력합니다.

```json
{
        "insecure-registries" : ["xxx.xxx.xxx.xxx:5000"]
}
```

* [Deploy a plain http registry](https://docs.docker.com/registry/insecure/#deploy-a-plain-http-registry)