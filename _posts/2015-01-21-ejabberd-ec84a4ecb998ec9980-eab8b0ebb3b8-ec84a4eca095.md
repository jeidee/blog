---
id: 22
title: ejabberd 설치와 기본 설정
date: 2015-01-21T23:40:42+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/ejabberd-%ec%84%a4%ec%b9%98%ec%99%80-%ea%b8%b0%eb%b3%b8-%ec%84%a4%ec%a0%95/'
permalink: '/2015/01/21/ejabberd-%ec%84%a4%ec%b9%98%ec%99%80-%ea%b8%b0%eb%b3%b8-%ec%84%a4%ec%a0%95/'
categories:
  - ejabberd
---
## 사전에 설치해야 하는 것 들

  * GNU Make
  * GCC
  * Libexpat 1.95 or higher
  * Erlang/OTP R15B or higher.
  * Libyaml 0.1.4 or higher
  * OpenSSL 0.9.8 or higher, for STARTTLS, SASL and SSL encryption.
  * Zlib 1.2.3 or higher, for Stream Compression support (XEP-01383
  
    ). Optional.
  * PAM library. Optional. For Pluggable Authentication Modules (PAM). See section 3.1.5.
  * GNU Iconv 1.8 or higher, for the IRC Transport (mod irc). Optional. Not needed on
  
    systems with GNU Libc. See section 3.3.8.
  * ImageMagick’s Convert program. Optional. For CAPTCHA challenges. See section 3.1.9

이 중에서 Libexpat, Erlang, Libyaml, Openssl은 꼭 설치한다.
  
모두 설치한 후 실행했는데 Libyaml과 관련해서 다음과 같은 오류가 생긴다면,

```
      
[error] unable to load p1\_yaml NIF: {error,{load\_failed,"Failed to load NIF library /lib/ejabberd/priv/lib/p1_yaml:
  
```

다음 문서를 참고해서 libyaml rpm을 받아 설치하면 된다.
  
http://blog.sina.com.cn/s/blog_96b8a1540101esch.html

## 리눅스계열 설치

### 설치

#### openssl 설치

```
      
wget ftp://ftp.openssl.org/source/openssl-1.0.0o.tar.gz
      
tar xvzf openssl-1.0.0o.tar.gz
      
cd openssl-1.0.0o
      
./config
      
sudo make && sudo make install
  
```

#### libyaml 설치

```
      
wget http://pyyaml.org/download/libyaml/yaml-0.1.6.tar.gz
      
tar xzvf yaml-0.1.6.tar.gz
      
cd yaml-0.1.6/
      
./configure
      
make
      
sudo make install
  
```

libyaml 관련 yaml.h와 library를 찾지 못한다면 다음과 같이 make 옵션을 준다.
  
(OSX에서 make할 때 libyaml을 설치했음에도 찾지 못하는 경우가 있다.)

```
      
sudo make CFLAGS="-I/usr/local/include" LDFLAGS="-L/usr/local/lib"
  
```

#### ejabberd 설치

```
      
git clone git://github.com/processone/ejabberd.git ejabberd
      
cd ejabberd
      
./configure
      
make
      
make install
  
```

mysql을 사용하고자 한다면 다음과 같이 해야 한다.
  
&#8211;prefix는 설치경로

```
      
./configure &#8211;enable-odbc &#8211;enable-mysql &#8211;prefix=/services/app/ejabberd
  
```

mysql 쿼리는 다음과 같이 실행해 줍니다.

```
      
cd ejabberd-14.07/sql
      
mysql -uroot -p
      
> create database ejabberd;
      
> grant all privileges on ejabberd.* to ejabberd@localhost identified by 'password' with grant option;
      
> grant all privileges on ejabberd.* to ejabberd@127.0.0.1 identified by 'password' with grant option;
      
> flush privileges;
      
> use ejabberd;
      
> source mysql.sql
  
```

### 실행

```
      
ejabberdctl start
      
or
      
ejabberdctl live
  
```

## 설치후 해야하는 것들

최근 버전의 ejabberd의 경우에는 설정파일을 yaml 포맷을 사용한다.
  
yaml의 경우 수정할 때 다음과 같은 사항을 유의해야 한다.

```
      
key:value # 유효하지 않음
      
key: value # 유효함
  
```

둘의 차이는 콜론(:)뒤에 value를 입력할 때 공백으로 간격이 있는지 여부이다.
  
이 외에도 유의할 사항이 있으면 계속 추가하겠다.

### 0. served host 추가

```
       
76 ### ================
       
77 ### SERVED HOSTNAMES
       
78
       
79 ##
       
80 ## hosts: Domains served by ejabberd.
       
81 ## You can define one or several, for example:
       
82 ## hosts:
       
83 ## &#8211; "example.net"
       
84 ## &#8211; "example.com"
       
85 ## &#8211; "example.org"
       
86 ##
       
87 hosts:
       
88 &#8211; "localhost"
       
89 &#8211; "example.org"
  
```

### admin 계정 추가

```
      
$ ejabberdctl register admin example.org 1234
  
```

### acl 수정

```
      
$ vi /etc/ejabberd/ejabberd.yml
  
```

acl:을 검색해서 다음과 같이 수정한다.

```
      
acl:
        
admin:
          
user:
            
&#8211; "admin": "example.org"
      
access:
        
configure:
          
admin: allow
  
```

만약 ip(예를 들어 192.168.101.101)나 추가 도메인이 필요하면,
  
다음과 같이 추가해 줘야 한다.

```
      
###. ====================
      
###' ACCESS CONTROL LISTS
      
acl:
        
admin:
         
user:
           
&#8211; "admin1": "192.168.101.101"
  
```

mysql 설정은 다음과 같이 한다.

```
      
$ vi /etc/ejabberd/ejabberd.yml

auth_method: odbc
      
\## auth_method: internal에서 변경한다.

\## MySQL server:
      
##
      
odbc_type: mysql
      
odbc_server: "localhost"
      
odbc_database: "ejabberd"
      
odbc_username: "ejabberd"
      
odbc_password: "ejabberd"
  
```

https://www.ejabberd.im/Using%20ejabberd%20with%20MySQL%20native%20driver

### TLS 설정

#### openssl 사용해서 pem 생성

https://www.ejabberd.im/tuto-install-ejabberd

#### ejabberd.yml 수정

## OSX에서 설치

```
  
export LDFLAGS="-L/usr/local/opt/openssl/lib -L/usr/local/lib -L/usr/local/opt/expat/lib"
  
export CFLAGS="-I/usr/local/opt/openssl/include/ -I/usr/local/include -I/usr/local/opt/expat/include"
  
export CPPFLAGS="-I/usr/local/opt/openssl/include/ -I/usr/local/include -I/usr/local/opt/expat/include"
  
```

자세한 내용은 다음 문서 참조

  * [ejabberd installation](https://docs.ejabberd.im/admin/guide/installation/)

## 관리 콘솔

http://127.0.0.1:5280/admin

## Reference

  * http://www.process-one.net/docs/ejabberd/guide_en.pdf