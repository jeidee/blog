---
id: 377
title: c++ 리눅스 데몬 프로세스 만들기
date: 2015-04-15T20:34:00+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=377
permalink: '/2015/04/15/c-%eb%a6%ac%eb%88%85%ec%8a%a4-%eb%8d%b0%eb%aa%ac-%ed%94%84%eb%a1%9c%ec%84%b8%ec%8a%a4-%eb%a7%8c%eb%93%a4%ea%b8%b0/'
categories:
  - c++
tags:
  - daemon
---
c/c++ 로 구현한 어플리케이션을 데몬 프로세스로 만들기 위해 다음과 같은 과정을 거쳐 추가 코드를 작성한다.

1) fork()를 사용해 자식프로세스를 생성 후 부모 프로세스를 종료
  
2) 터미널 종료시 SIGHUP 시그널 무시
  
3) (선택) 루트 디렉토리로 경로 변경
  
4) setsid()로 새로운 세션 생성
  
5) while(true) 루프 생성

```cpp
  
#include <sys/types.h>
  
#include <sys/stat.h>
  
#include <stdio.h>
  
#include <fcntl.h>
  
#include <signal.h>
  
#include <stdlib.h>

int main()
  
{
      
pid_t pid;

// fork 에러
      
if (( pid = fork()) < 0)
          
exit(0);
      
// 정상 : 부모프로세스를 종료한다.
      
else if(pid != 0)
          
exit(0);

// 터미널 종료 시 signal의 영향을 받지 않도록 처리
      
signal(SIGHUP, SIG_IGN);
      
close(0);
      
close(1);
      
close(2);

chdir("/");

setsid(); 

// 여기에 프로그램 본체를 넣는다.
      
while(1)
      
{
          
sleep(1);
      
}
      
exit(0);
  
}
  
```

실행 후 프로세스를 살펴보면 PPID가 1(ubuntu에서 upstart를 사용할 경우 upstart pid)인 것을 확인할 수 있다.
  
init 프로세스의 pid가 1이다.

## 참고

  * [데몬 프로그램에 대한 이해](http://www.joinc.co.kr/modules/moniwiki/wiki.php/article/%B5%A5%B8%F3(daemon)%20%C7%C1%B7%CE%B1%D7%B7%A5%BF%A1%20%B4%EB%C7%D1%20%C0%CC%C7%D8)
  * [Linux Daemon 만들기](http://www.freezner.com/archives/503)
  * [Linux Daemon 프로그램 제작방법](http://realmind.tistory.com/entry/linux-Daemon%EB%8D%B0%EB%AA%AC-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A0%9C%EC%9E%91%EB%B0%A9%EB%B2%95)