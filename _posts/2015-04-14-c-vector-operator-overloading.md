---
id: 385
title: c++ vector == operator overloading
date: 2015-04-14T20:40:00+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=385
permalink: /2015/04/14/c-vector-operator-overloading/
categories:
  - c++
tags:
  - operator overloading
  - vector
---
```cpp
  
bool operator==(vector<int>& lhs, vector<int>& rhs) {
      
if (lhs.size() != rhs.size()) return false;

for (vector<int>::iterator lit = lhs.begin(), rit = rhs.begin();
              
lit != lhs.end() && rit != rhs.end();
              
++lit, ++rit) {
          
if (\*lit != \*rit) return false;
      
}

return true;
  
}
  
```

주의할 것은 클래스 내부가 아닌 외부에서 연산자를 오버로딩할 때 bool operator==() **const** { &#8230; } 와 같이 하는 경우 다음과 같은 컴파일 에러를 만날 수 있다.

> non-member function cannot have cv-qualifier&#8230; 

원인은 **const** { &#8230; }의 경우 {} 블럭 안에서 클래스의 멤버 변수를 변경하지 않겠다고 명시하는 것이기 때문이며, const를 제거해 주면 해결된다.