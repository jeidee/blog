---
author: jeidee
categories:
- aws
- go
date: '2016-12-21'
guid: https://erlnote.wordpress.com/?p=1658
id: 1658
permalink: /2016/12/21/aws-credentials%ea%b0%80-upstart-%eb%93%b1%ec%97%90%ec%84%9c-%ec%a0%9c%eb%8c%80%eb%a1%9c-%eb%8f%99%ec%9e%91%ed%95%98%ec%a7%80-%ec%95%8a%ec%9d%84-%ea%b2%bd%ec%9a%b0/
tags:
- aws-sdk-go
- upstart
title: AWS Credentials가 upstart 등에서 제대로 동작하지 않을 경우
url: /2016/12/21/aws-credentialseab080-upstart-eb93b1ec9790ec849c-eca09ceb8c80eba19c-eb8f99ec9e91ed9598eca780-ec958aec9d84-eab2bdec9ab0
---

~/.aws/credentials 파일에 AWS의 Credentials Key를 설정해 놓고 개발하다가,
  
upstart나 superviosord등을 사용해 실행하게 되면 다음과 같은 에러가 발생한다.

```
  
NoCredentialProviders: no valid providers in chain. Deprecated.
  
```

이럴 경우 다음과 같이 AWS Credentials 정보를 환경변수로 설정해서 사용하면 문제 없이 사용 가능하다.
  
관련 정보는 [링크](https://github.com/aws/aws-sdk-go#configuring-credentials)를 참고한다.

https://gist.github.com/34468d32d6688884f0319266ed8792fc.git