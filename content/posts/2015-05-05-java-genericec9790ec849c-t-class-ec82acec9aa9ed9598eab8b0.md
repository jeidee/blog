---
author: jeidee
categories:
- java
date: '2015-05-05'
guid: https://erlnote.wordpress.com/?p=345
id: 345
permalink: /2015/05/05/java-generic%ec%97%90%ec%84%9c-t-class-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/
tags:
- generic
- reflection
title: java generic에서 T.class 사용하기
url: /2015/05/05/java-genericec9790ec849c-t-class-ec82acec9aa9ed9598eab8b0
---

Class 클래스는 T 클래스의 메타정보를 갖는 클래스이다.
  
T가 Temp라는 사용자 정의 클래스라고 할 때,
  
Temp.class와 Class.forName(&#8220;com.example.Temp&#8221;)은 Class 오브젝트를 반환한다.
  
이 Class 오브젝트를 사용해 Temp 클래스의 인스턴스를 생성할 수 있고, 메소드와 필드를 Reflection할 수 있다.

Class를 사용해 T의 새로운 인스턴스를 생성하고자 할 때는 다음과 같이 newInstance()를 사용할 수 있다.

```java
  
Temp temp = (Temp) Temp.class.newInstance();
  
Temp temp2 = (Temp) Class.forName("com.example.Temp").newInstance();
  
```

문제는 다음과 같은 Generic 클래스를 정의할 때 타입 T의 Class 오브젝트를 T.class와 같이 사용할 수 없기 때문에 발생한다.

```java
  
public class MyGeneric<T> {
      
public void someMethod() {
          
Temp temp = (Temp) T.class.newInstance(); // 에러
      
}
  
}
  
```

이럴 때는 다음과 같이 Constructor에 T의 타입클래스를 매개변수로 전달해서 사용할 수 있다.

```java
  
public class MyGeneric<T> {
      
final Class<T> mTTypeClass;

public MyGeneric(Class<T> tTypeClass) {
          
mTTypeClass = tTypeClass;
      
}
  
```

## 참고

  * [how to get class instance of generics type T](http://stackoverflow.com/questions/3437897/how-to-get-class-instance-of-generics-type-t)
  * [java.lang.Class](http://noritersand.tistory.com/59)