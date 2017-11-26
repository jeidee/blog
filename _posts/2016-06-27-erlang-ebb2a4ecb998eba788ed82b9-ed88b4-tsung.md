---
id: 1516
title: erlang 벤치마킹 툴 tsung
date: 2016-06-27T12:44:49+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1516
permalink: '/2016/06/27/erlang-%eb%b2%a4%ec%b9%98%eb%a7%88%ed%82%b9-%ed%88%b4-tsung/'
categories:
  - utility
tags:
  - benchmark
  - load test
---
[Tsung](http://tsung.erlang-projects.org/)은 오픈소스 분산 테스팅 도구이다.
  
http, xmpp등을 테스트할 수 있으며 Client Agent와 서버 모니터링 도구를 제공하고, 결과는 다양한 그래프로 출력해 준다.

## 설치

### centos 6

  * [참고](https://gist.github.com/aviafelix/f6a591ed8e4e3705661a)

```
  
yum -y install erlang perl gnuplot perl-Template-Toolkit

wget http://tsung.erlang-projects.org/dist/tsung-1.6.0.tar.gz

tar zxfv tsung-1.6.0.tar.gz
  
cd tsung-1.6.0
  
./configure &#8211;prefix=/usr/local/ && make && make install

cp /usr/share/doc/tsung/examples/http_simple.xml /root/.tsung/tsung.xml
  
```

### ubuntu

```
  
sudo apt-get install erlang
  
sudo apt-get install tsung
  
```

## 실행

### 간단한 http 테스팅

위와 같이 설치하고 나면 다음 경로에서 예제 테스팅 템플릿 파일을 찾을 수 있다.

```
  
/usr/local/share/doc/tsung/examples
  
```

http_simple.xml파일을 다음과 같이 복사한다.

```
  
mkdir ~/.tsung
  
cp /usr/local/share/doc/tsung/examples/http_simple.xml ~/.tsung/tsung.xml
  
```

#### tsung.xml 수정하기

tsung.xml을 열어보면 다음과 같은 영역으로 구분되어 있다.

  * tsung : 테스트 시나리오는 tsung 태그 안에 작성한다.</p> 
  * clients : tsung이 설치된 테스트 클라이언트 정보를 등록한다.

  * servers : 테스트 대상 서버 정보를 입력한다. 가중치를 입력해서 테스트 부하를 분산할 수 있다.

  * monitoring : CPU, Memory등의 OS 사용량을 모니터링하고자 하는 대상 서버 정보를 입력한다.

  * load : 테스트 유저의 생성 주기와 최대 유저수를 phase단위로 달리 설정할 수 있다.

  * options : 전역 변수를 설정한다.

  * sessions : 연결 세션별로 테스트 시나리오를 작성할 수 있다. 발생확률(probability)을 조정해서 시나리오 발생비율을 조정할 수 있다.

좀 더 자세한 내용은 [Tsung Document](http://tsung.erlang-projects.org/user_manual/index.html)를 참고한다.

http_simple.xml의 내용을 다음과 같이 수정해 보자.

> <clients>
        
> <client host="localhost" use\_controller\_vm="true"/>
      
> </clients> 

tsung 클라이언트 설정이다. 여러대의 클라이언트를 설정할 수 있다.
  
위의 예는 tsung이 실행되는 동일 host와 Erlang Runtime에서 클라이언트가 실행된다는 것을 의미한다.

remote client를 사용해 controller -> worker 형태의 구성을 하고자 한다면,
  
다음 문서를 참고한다.

  * [Easy distributed load test with Tsung](http://www.slideshare.net/ngocdaothanh/tsung-13985127)
  * [Can't start distributed clients: timeout error](http://tsung.erlang-projects.org/user_manual/faq.html#can-t-start-distributed-clients-timeout-error)

위의 내용을 간단히 요약하면 다음과 같다.
  
* 모든 client node에는 동일 버전의 erlang과 tsung이 설치되어 있어야 한다.
  
* erl과 tsung은 path에 지정되어 있어야 한다.
  
* 모든 client node는 firewall이 해제되어 있어야 하고, ssh로 암호 없이 접속 가능해야 한다.
  
* 모든 client node는 host이름이 /etc/hosts에 정상적으로 등록되어 있어야 하며, 서로 해당 host명으로 접속할 수 있어야 한다.

위의 조건은 아래 monitoring 대상 서버를 등록할 때에도 동일하게 적용된다.

> <servers>
      
> <server host="localhost" port="80" type="tcp"></server>
    
> </servers> 

웹서버가 로컬호스트에 있을 경우 위와 같이 작성하면 된다.
  
위 정보는 http request를 할 때 URL의 host 정보에 해당한다.

> <monitoring>
        
> <monitor host="foo" type="erlang"></monitor>
        
> <monitor host="bar" type="erlang"></monitor>
      
> </monitoring> 

monitoring 항목에 monitor 대상 서버를 등록하면 대상 서버의 CPU와 Memory 사용량을 모니터링하게 된다.
  
monitoring 대상 서버에는 위 client 항목에서 명시된 조건이 충족되어야 함을 주의하도록 하자.

> <load>
       
> <arrivalphase phase="1" duration="10" unit="minute">
         
> <users interarrival="2" unit="second"></users>
       
> </arrivalphase>
      
> </load> 

load 항목에는 테스트 부하를 설정한다.
  
arrivalphase는 순차적으로 실행되는 테스트 단계라고 보면 되며 duration 기간동안 session에 명시된 시나리오를 반복 수행하게 된다.
  
users 항목은 해당 phase동안 interarrival 간격으로 신규 user를 생성시키도록 설정한다.

즉, 위 설정은 10분 동안 매 2초 마다 한 명의 user를 생성해서 테스트를 수행하라는 의미이다.

> <options>
       
> <option type="ts\_http" name="user\_agent">
        
> <user\_agent probability="80">Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.7.8) Gecko/20050513 Galeon/1.3.21</user\_agent>
        
> <user\_agent probability="20">Mozilla/5.0 (Windows; U; Windows NT 5.2; fr-FR; rv:1.7.8) Gecko/20050511 Firefox/1.0.4</user\_agent>
       
> </option>
      
> </options> 

option 항목에는 http request할 때 임의로 정의할 user_agent를 설정한다.
  
probability의 합은 100이어야 하고, probability는 발생비율을 조정한다.

> <sessions>
      
> <session name="http-example" probability="100" type="ts_http">
        
> <request> <http url="/" method="GET" version="1.1"></http> </request>
        
> <thinktime value="20" random="true"></thinktime>
      
> </session>
     
> </sessions> 

sessions에는 테스트 시나리오를 작성한다.
  
session의 probability 값의 합은 100이어야 하고 발생비율을 조정한다.
  
request는 http 요청을 의미하며 url은 server항목에 지정된 host와 port를 사용해서 전체 url이 완성된다.
  
thinktime은 실행과 실행 사이에 간격을 의미하며 단위는 seconds이다.

하나의 시나리오는 한 명의 user가 해당 phase기간 동안 반복 수행하게 된다.

### 실행

다음과 같이 실행 한다.

```
  
tsung -f ~/.tsung/http_simple.xml start
  
```

### 웹 GUI

실행 되는 동안 웹브라우저에서 GUI를 통해 tsung의 동작 상태와 보고서(그래프 포함)를 확인할 수 있다.

기본포트는 8091이다.
  
tsung 컨트롤러가 실행 중인 host가 localhost일 경우, 다음 URL을 웹브라우저에서 탐색할 수 있다.

```
  
http://localhost:8091
  
```

## 보고서 생성

테스트가 완료되면 해당 로그 디렉토리로 이동해서 다음 스크립트를 실행한다.

```
  
cd ~/.tsung/log/20160629-0202
  
/usr/local/lib/tsung/bin/tsung_stats.pl
  
```

tsung_stats.pl은 perl script파일이다. 경로는 시스템마다 조금씩 다를 수 있다.

해당 스크립트 실행 매개변수와 보고서의 상세항목에 대한 설명은 다음 문서를 참고하도록 한다.

  * [Statistics and Reports](http://tsung.erlang-projects.org/user_manual/reports.html)