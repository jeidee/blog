---
author: jeidee
categories:
- utility
date: '2015-01-22'
guid: https://erlnote.wordpress.com/2015/01/22/git-proxy-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/
id: 49
permalink: /2015/01/22/git-proxy-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/
tags:
- git
- proxy
title: git proxy 사용하기
url: /2015/01/22/git-proxy-ec82acec9aa9ed9598eab8b0
---

## git proxy 설정

### corkscrew 사용할 경우

먼저 git proxy를 사용하기 위해 corkscrew를 설치한다.

```
      
$ sudo apt-get install corkscrew
  
```

그리고 proxy shell 스크립트를 다음과 같이 생성한다.

```
      
$ vi ~/git-proxy.sh
  
```

git-proxy.sh 파일을 다음과 같이 작성하고

```
      
#!/bin/bash
      
/usr/bin/corkscrew proxyIP proxyPort $1 $2
  
```

git-proxy.sh파일에 실행권한을 준다.

```
      
$ chmod u+x ~/git-proxy.sh
  
```

마지막으로 다음과 같이 gitconfig를 수정한다.

```
      
$ vi ~/.gitconfig

[core]
        
gitproxy="~/git-proxy.sh"
  
```

~부분은 절대경로로 수정한다.

git http proxy 설정

다음과 같이 설정을 진행하면 된다.

```
      
git config &#8211;global http.proxy 192.168.0.1:8080
  
```

### socat 사용할 경우(centos에서)

1) epel repo를 설치한다.

```
   
rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
   
yum update
  
```

2) socat을 설치한다.

```
   
yum install socat
  
```

3) gitproxy 쉘 스크립트를 다음과 같이 작성한다.

```
  
#!/bin/sh
  
\# Use socat to proxy git through an HTTP CONNECT firewall.
  
\# Useful if you are trying to clone git:// from inside a company.
  
\# Requires that the proxy allows CONNECT to port 9418.
  
#
  
\# Save this file as gitproxy somewhere in your path (e.g., ~/bin) and then run
  
\# chmod +x gitproxy
  
\# git config &#8211;global core.gitproxy gitproxy
  
#
  
\# More details at http://tinyurl.com/8xvpny
  
\# Configuration. Common proxy ports are 3128, 8123, 8000.

_proxy=192.168.x.x
  
_proxyport=xxxx

#DEBUG="-d -d -d -d"
  
exec /usr/bin/socat $DEBUG &#8211; PROXY:$\_proxy:$1:$2,proxyport=$\_proxyport
  
```

4) ~/.gitconfig를 다음과 같이 수정한다.

```
  
[http]
          
proxy = 192.168.x.x:xxxx
  
[https]
          
proxy = 192.168.x.x:xxxx
  
[ssh]
          
proxy = 192.168.x.x:xxxx
  
[core]
          
gitproxy = /root/gitproxy
  
```