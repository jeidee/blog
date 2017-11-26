---
id: 46
title: mysql root 계정 초기화
date: 2015-01-22T00:10:22+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/22/mysql-root-%ea%b3%84%ec%a0%95-%ec%b4%88%ea%b8%b0%ed%99%94/'
permalink: '/2015/01/22/mysql-root-%ea%b3%84%ec%a0%95-%ec%b4%88%ea%b8%b0%ed%99%94/'
categories:
  - utility
tags:
  - mysql
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