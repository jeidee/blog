---
id: 1039
title: osx + eclipse CDT에서 boost설정
date: 2016-04-01T16:14:31+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=1039
permalink: '/2016/04/01/osx-eclipse-cdt%ec%97%90%ec%84%9c-boost%ec%84%a4%ec%a0%95/'
categories:
  - c++
tags:
  - boost
---
## osx에서 boost 설치

```
  
$ brew install boost
  
```

## sample source

```cpp
  
#include <boost/regex.hpp>
  
#include <iostream>
  
#include <string>

int main()
  
{
      
std::string line;
      
boost::regex pat( "^Subject: (Re: |Aw: )\*(.\*)" );

while (std::cin)
      
{
          
std::getline(std::cin, line);
          
boost::smatch matches;
          
if (boost::regex_match(line, matches, pat))
              
std::cout << matches[2] << std::endl;
      
}
  
}
  
```

## 프로젝트 설정

  1. 프로젝트의 속성을 연다.
  2. C/C++ Build > Settings > Tool Settings > GCC C++ Linker > Libraries를 선택한다.
  3. Libraries(-l) 항목에 boost\_regex를 입력한다. (*주의: 실제 라이브러리 파일명은 libboost\_regex-mt.a, &#8230;등이다.)
  4. Library search path(-L)에 /usr/local/Cellar/boost/1.60.0_1/lib(또는 /usr/local/lib에서 라이브러리 파일이 심볼릭링크된 경우 생략 가능)

## 빌드

빌드한 후 에러 없이 성공하면 완료!