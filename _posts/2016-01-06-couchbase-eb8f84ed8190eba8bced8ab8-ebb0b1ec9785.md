---
author: jeidee
categories:
- couchbase
date: '2016-01-06'
guid: https://erlnote.wordpress.com/?p=822
id: 822
permalink: /2016/01/06/couchbase-%eb%8f%84%ed%81%90%eb%a8%bc%ed%8a%b8-%eb%b0%b1%ec%97%85/
tags:
- backup
title: couchbase 도큐먼트 백업
url: /2016/01/06/couchbase-eb8f84ed8190eba8bced8ab8-ebb0b1ec9785
---

```
  
cbbackup http://[host]:8091 [backup-location] -x design\_doc\_only=1 -b [bucket-name]
  
```

  * [출처](http://docs.couchbase.com/admin/admin/CLI/cbbackup-ddocs.html)