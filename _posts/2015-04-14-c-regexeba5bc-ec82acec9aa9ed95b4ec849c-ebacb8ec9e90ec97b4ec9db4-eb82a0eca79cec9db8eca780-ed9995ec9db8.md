---
author: jeidee
categories:
- c++
date: '2015-04-14'
guid: https://erlnote.wordpress.com/?p=379
id: 379
permalink: /2015/04/14/c-regex%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-%eb%ac%b8%ec%9e%90%ec%97%b4%ec%9d%b4-%eb%82%a0%ec%a7%9c%ec%9d%b8%ec%a7%80-%ed%99%95%ec%9d%b8/
tags:
- regex
title: c++ regex를 사용해서 문자열이 날짜인지 확인
url: /2015/04/14/c-regexeba5bc-ec82acec9aa9ed95b4ec849c-ebacb8ec9e90ec97b4ec9db4-eb82a0eca79cec9db8eca780-ed9995ec9db8
---

정규표현식을 사용해서 주어진 문자열이 날짜인지 확인하는 코드는 다음과 같다.

```cpp
  
#include <string>
  
#include <iostream>
  
#include <regex>

using namespace std;

bool string\_util::is\_date(string source) {
      
// yyyy-mm-dd 형식의 날짜만 지원
      
regex ex("d{4}-d{2}-d{2}");
      
smatch matches;

while (regex_search(source, matches, ex)) {
          
for (auto m : matches) {
              
cout << "match string is " << m << endl;
          
}

return true;
      
}

return false;
  
}

```

굳이 while (regex\_search(&#8230;)) 루프를 사용하지 않고 if (regex\_search())를 사용해도 되겠지만, 용법을 살펴보는 의미로 추가했다.

또한 제한된 날짜 포맷(yyyy-mm-dd)만 체크 가능하며 9999-99-99와 같이 유효하지 않은 날짜도 패스시키는 단점이 있으니 필요할 경우 보강해서 사용하도록 한다.

참고 링크에는 boost의 posix_time을 사용해서 다양한 날짜와 시간 포맷의 문자열을 처리하는 방법도 있다.

## 참고

  * [How to parse date/time from string?](http://stackoverflow.com/questions/3786201/how-to-parse-date-time-from-string)
  * [regex_search](http://www.cplusplus.com/reference/regex/regex_search/)