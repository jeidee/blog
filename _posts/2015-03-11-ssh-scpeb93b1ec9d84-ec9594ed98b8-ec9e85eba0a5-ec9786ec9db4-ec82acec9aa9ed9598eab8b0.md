---
id: 276
title: ssh, scp등을 암호 입력 없이 사용하기
date: 2015-03-11T09:18:22+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=276
permalink: '/2015/03/11/ssh-scp%eb%93%b1%ec%9d%84-%ec%95%94%ed%98%b8-%ec%9e%85%eb%a0%a5-%ec%97%86%ec%9d%b4-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/'
categories:
  - utility
tags:
  - scp
  - ssh
---
다음의 순서로 진행한다.

1) 키 생성

```
  
$ ssh-keygen -t rsa
  
```

키 생성할 때는 passphrase를 빈 값으로(아무 값도 입력하지 않고 엔터) 입력해야 함을 주의하자.

2) 공개키 리포트 서버에 등록

```
  
$ ssh-copy-id -i ~/.ssh/id_rsa.pub [remote-host]
  
```

여기까지 하면 완료되었다.

다음과 같이 암호 입력없이 정상 접속 되는지 확인해 본다.

```
  
$ ssh [remote-host]
  
```

만약 다음과 같은 에러가 출력된다면,

```
  
Agent admitted failure to sign using the key.
  
```

다음과 같이 ssh 사용을 활성화한다.

```
  
$ ssh-add
  
```