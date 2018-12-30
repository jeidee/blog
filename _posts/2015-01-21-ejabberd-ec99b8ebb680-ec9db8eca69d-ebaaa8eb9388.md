---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-%ec%99%b8%eb%b6%80-%ec%9d%b8%ec%a6%9d-%eb%aa%a8%eb%93%88/
id: 21
permalink: /2015/01/21/ejabberd-%ec%99%b8%eb%b6%80-%ec%9d%b8%ec%a6%9d-%eb%aa%a8%eb%93%88/
title: ejabberd 외부 인증 모듈
url: /2015/01/21/ejabberd-ec99b8ebb680-ec9db8eca69d-ebaaa8eb9388
---

## ejabberd 외부 인증 모듈

  * [참고문서](https://www.ejabberd.im/files/doc/dev.html#htoc9)

### 개요

외부 인증 스크립트는 [erlang port driver API](http://www.erlang.org/doc/tutorial/c_portdriver.html)를 따릅니다.

스크립트는 다음과 같은 일을 수행하는 무한 루프로 구성해야 합니다:

  * stdin에서 AABBBBBB&#8230;.형식의 byte stream을 읽는다. 
      * A: 데이터의 길이를 의미하는 2bytes 정수(네트워크 바이트 오더 사용)
      * B: 다음과 같은 plain 텍스트 오퍼레이션을 포함 
          * auth:User:Server:Password(username/password 검사)
          * isuser:User:Server(유효한 user인지 검사)
          * setpass:User:Server:Password(user의 암호 설정)
          * tryregister:User:Server:Password(계정 등록 시도)
          * removeuser:User:Server(계정 삭제)
          * removeuser3:User:Server:Password(암호가 유효하면 계정 삭제)
  * stdout에 결과 출력: AABB 
      * A: 다음 결과값에 대한 길이. 2bytes 정수(short)
      * B: 결과 코드(short). 1은 성공/유효, 0은 실패/유효하지 않음.

### 샘플코드

#### python

```python
      
#!/usr/bin/python

import sys
      
from struct import *

def from_ejabberd():
          
input_length = sys.stdin.read(2)
          
(size,) = unpack('>h', input_length)
          
return sys.stdin.read(size).split(':')

def to_ejabberd(bool):
          
answer = 0
          
if bool:
              
answer = 1
          
token = pack('>hh', 2, answer)
          
sys.stdout.write(token)
          
sys.stdout.flush()

def auth(username, server, password):
          
return True

def isuser(username, server):
          
return True

def setpass(username, server, password):
          
return True

while True:
          
data = from_ejabberd()
          
success = False
          
if data[0] == "auth":
              
success = auth(data[1], data[2], data[3])
          
elif data[0] == "isuser":
              
success = isuser(data[1], data[2])
          
elif data[0] == "setpass":
              
success = setpass(data[1], data[2], data[3])
          
to_ejabberd(success)
  
```

#### node.js

node.js용 ejabberd auth 모듈이 여럿 있습니다.
  
그 중 [ejabberd-auth](https://github.com/derWhity/node-ejabberd-auth) 모듈은 다음과 같이 사용합니다.

##### 1. 모듈 설치

```
      
npm install ejabberd-auth
  
```

##### 2. auth.js 작성

```javascript
      
require('ejabberd-auth').run({
          
actions: {
              
auth: function(done, userName, domain, password) {
                  
// Some auth to be done here
                  
done(true); // or done(false) if the authentification has failed
              
}
          
}
      
});
  
```

위 소스는 모든 인증 요청을 통과시킵니다.

### 설정

node.js용 인증모듈을 /etc/ejabberd/external_auth/node 디렉토리에 auth.js 파일이름으로 작성합니다.
  
package.json파일을 작성하지 않을 경우 다음과 같이 ejabberd-auth 모듈을 인스톨합니다.

```
      
npm install ejabberd-auth
  
```

/etc/ejabberd/ejabberd.yml파일을 열고 auth_method를 다음과 같이 수정합니다.

```
      
auth_method: external
      
extauth\_program: "node /etc/ejabberd/external\_auth/node/auth.js"
  
```

ejabberd를 재실행하면 모든 인증요청이 통과됩니다.