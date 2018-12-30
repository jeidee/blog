---
author: jeidee
categories:
- c++
date: '2016-04-05'
guid: https://erlnote.wordpress.com/?p=1050
id: 1050
permalink: /2016/04/05/visual-c-for-linux-development/
title: Visual C++ for Linux Development
url: /2016/04/05/visual-c-for-linux-development
---

Windows 환경의 Visual Studio 2015에서 리눅스용 C++ 어플리케이션을 개발할 수 있게 되었다.
  
ssh를 통해 Remote 개발을 하는 구성인데 다음 링크에서 자세한 내용을 확인할 수 있다.

<blockquote class="wp-embedded-content" data-secret="U4UHc125Qg">
  <p>
    <a href="https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/">Visual C++ for Linux Development</a>
  </p>
</blockquote>



해당 링크의 내용을 따라서 다음과 같이 진행해 보았다.

### 1) 우분투 설정

```
  
$ sudo apt-get install openssh-server g++ gdb gdbserver
  
```

### 2) Visual Studio 2015용 확장 기능 설치

https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e

### 3) Visual Studio에서 Linux용 C++ 프로젝트 생성

New Project > Templates > Visual C++ > Cross Platform > Linux > Empty Project(Linux)

### 4) Remote GDB Debugger 설정

솔루션구성 > 솔루션플랫폼을 각각 Debug > x64로 설정하고 디버거가 Remote GDB Debugger로 선택되어 있는지 확인한다.

Remote GDB Debugger를 클릭하면 Linux 연결설정 팝업이 출력되는데 다음과 같이 항목을 채운 후 연결하도록 한다.

  * Host name : 리눅스 서버 IP
  * Port : SSH 서버 Port(기본값 22)
  * User name : 리눅스 계정
  * Authentication Type : 인증 유형(기본값 Password)
  * Password : 리눅스 계정 암호

만약 설정이 잘못되었다면 다음 메뉴를 통해 연결 설정을 편집할 수 있다.

Tools > Options > Cross Platform > Linux

### 5) main.cpp 작성

&#8220;Hello, World!&#8221;를 찍는 간단한 main.cpp 파일을 작성한다.

```cpp
  
#include <cstdio>

int main() {
      
printf(&quot;Hello, World!&quot;);
      
return 0;
  
}
  
```

### 6) 빌드 및 디버깅

빌드를 하거나 디버깅을 선택하면 설정에 문제가 없을 경우 정상적으로 작업이 수행되는 것을 확인할 수 있다.
  
타겟 리눅스 서버의 홈 디렉토리의 ~/projects/ 경로에서 해당 프로젝트의 소스파일과 빌드 결과물을 확인할 수 있다.

### 7) 기타 프로젝트 설정

실행파일뿐만 아니라 Dynamic Library(.so), Static Library(.a)등도 선택할 수 있다.
  
또한 GUI 어플리케이션도 개발할 수 있는데, 관련해서 자세한 내용은 원문을 참고하도록 한다.