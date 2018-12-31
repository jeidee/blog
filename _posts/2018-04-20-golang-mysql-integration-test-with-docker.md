---
title: "Golang, Docker 사용해서 MySQL 통합 테스트"
date: 2018-04-20T10:07:39+09:00
author: jeidee
categories:
- go
tags:
- docker
- mysql
- golang
---

다음과 같이 shell script를 작성한다.

```
#!/bin/bash

docker run --rm --name mysqltest -e MYSQL_ROOT_PASSWORD=root --health-cmd='mysqladmin ping --silent' -p 3306:3306 -d mysql

function getContainerHealth {
  docker inspect --format "{{json .State.Health.Status}}" $1
}

function waitContainer {
  while STATUS=$(getContainerHealth $1); [ $STATUS != "\"healthy\"" ]; do
    if [ $STATUS == "\"unhealthy\"" ]; then
      echo "Failed!"
      exit -1
    fi

    printf .
    lf=$'\n'
    sleep 1
  done

  printf "$1f"
}

waitContainer mysqltest
go test ./...
docker stop mysqltest
```

우선 mysql container를 실행할 때 --health-cmd='mysqladmin ping --silent' 옵션을 추가해야 한다.

이후 docker inspect를 통해 State.Health.Status를 확인하면 mysql의 구동 상태를 확인할 수 있다.

해당 상태값이 "healthy"가 될 때까지 기다린 후 테스트를 실행하면 되는데,
그 전에 테스트가 실행되면 bad connection에러를 반환하고 mysql 서버에 연결 실패하게 된다.

참고
* https://stackoverflow.com/questions/25503412/how-do-i-know-when-my-docker-mysql-container-is-up-and-mysql-is-ready-for-taking?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa