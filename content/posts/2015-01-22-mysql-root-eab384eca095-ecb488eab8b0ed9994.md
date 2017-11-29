---
author: jeidee
categories:
- utility
date: '2015-01-22'
guid: https://erlnote.wordpress.com/2015/01/22/mysql-root-%ea%b3%84%ec%a0%95-%ec%b4%88%ea%b8%b0%ed%99%94/
id: 46
permalink: /2015/01/22/mysql-root-%ea%b3%84%ec%a0%95-%ec%b4%88%ea%b8%b0%ed%99%94/
tags:
- mysql
title: mysql root 계정 초기화
url: /2015/01/22/mysql-root-eab384eca095-ecb488eab8b0ed9994
---

mysql root 계정의 패스워드를 분실했을 경우 다음 절차를 따라 root 계정의 패스워드를 변경할 수 있다.

```
      
$ service mysql stop
      
$ mysqld\_safe &#8211;skip\_grant &
      
$ mysql
      
mysql> use mysql;
      
mysql> update user set password=password('root') where user='root';
      
mysql> flush privileges;
  
```