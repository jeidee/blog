---
author: jeidee
categories:
- c++
date: '2015-04-14'
guid: https://erlnote.wordpress.com/?p=383
id: 383
permalink: /2015/04/14/c-ubuntu%ec%97%90%ec%84%9c-mysql-%ea%b0%9c%eb%b0%9c-%ec%97%b0%ea%b2%b0/
tags:
- mysql
title: c++ ubuntu에서 mysql 개발 &#8211; 연결
url: /2015/04/14/c-ubuntuec9790ec849c-mysql-eab09cebb09c-ec97b0eab2b0
---

ubuntu에서 mysql 개발을 위해서, 먼저 mysql library를 다음과 같이 설치한다.

```
  
sudo apt-get install libmysqlcppconn-dev
  
```

그런 후 다음과 같이 단순 연결 코드를 작성한다. (test.cpp)

```cpp
  
#include <iostream>
  
#include "mysql_driver.h"

int main() {
      
sql::mysql::MySQL_Driver *driver;
      
sql::Connection *con;

driver = sql::mysql::get\_mysql\_driver_instance();

con = driver->connect("tcp://127.0.0.1:3306", "ejabberd", "ejabberd");

delete con;

return 0;
  
}
  
```

g++을 사용해서 다음과 같이 컴파일 한다.

```
  
g++ -Wall -I/usr/include/cppconn -o test test.cpp -L/usr/lib -lmysqlcppconn
  
```

## 참고

  * [MySQL Connector/C++ Developer Guide](http://dev.mysql.com/doc/connector-cpp/en/)
  * [C++ / mysql Connector &#8211; undefined reference to get\_driver\_instance &#8211; already tried the easy stuff](http://stackoverflow.com/questions/15995319/c-mysql-connector-undefined-reference-to-get-driver-instance-already-tri)