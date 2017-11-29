---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-mysql-odbc-%ec%84%a4%ec%a0%95/
id: 34
permalink: /2015/01/21/ejabberd-mysql-odbc-%ec%84%a4%ec%a0%95/
tags:
- mysql
title: ejabberd mysql odbc 설정
url: /2015/01/21/ejabberd-mysql-odbc-ec84a4eca095
---

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

위와 같이 한 후 모듈별로 db_type을 설정해야 하는데, roster와 vcard를 mysql에 저장하도록 하려면 다음과 같이 ejabberd.yml파일을 수정한다.

```
        
mod_roster:
          
db_type: odbc
        
mod\_shared\_roster: {}
        
mod_stats: {}
        
mod_time: {}
        
mod_vcard:
          
db_type: odbc
  
```