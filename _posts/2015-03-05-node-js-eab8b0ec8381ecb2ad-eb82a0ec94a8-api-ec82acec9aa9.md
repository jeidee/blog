---
author: jeidee
categories:
- node.js
date: '2015-03-05'
guid: https://erlnote.wordpress.com/?p=270
id: 270
permalink: /2015/03/05/node-js-%ea%b8%b0%ec%83%81%ec%b2%ad-%eb%82%a0%ec%94%a8-api-%ec%82%ac%ec%9a%a9/
tags:
- open api
- 날씨API
title: node.js 기상청 날씨 api 사용
url: /2015/03/05/node-js-eab8b0ec8381ecb2ad-eb82a0ec94a8-api-ec82acec9aa9
---

기상청에서는 동 단위의 날씨 예보를 API로 제공하고 있다.
  
기상청 API를 사용해서 동네 날씨 예보를 구하는 방법은 좌표를 사용하는 방법과 법정동코드를 사용하는 방법 두 가지가 있다.

## 날씨 조회

이 중에서 좌표를 사용하는 방법을 알아보자.
  
(좌표는 위경도가 아닌 구역 그리드 좌표인 것 같다.)

먼저 도/시, 시/군/구, 읍/면/동 정보로 좌표를 구하는 방법은 다음과 같다.

1) 도/시 정보 구하기
  
다음 URL을 사용해서 json 포맷의 도/시 정보를 구한다.
  
http://www.kma.go.kr/DFSROOT/POINT/DATA/top.json.txt

아래와 같은 json 포맷의 데이터를 구할 수 있다.

```
  
[
  
{
  
code: "11",
  
value: "서울특별시"
  
},
  
{
  
code: "26",
  
value: "부산광역시"
  
},
  
```

2) 서울특별시의 시/군/구 정보를 구하기
  
다음 URL을 사용해서 json 포맷의 시/군/구 정보를 구한다.
  
&#8216;http://www.kma.go.kr/DFSROOT/POINT/DATA/mdl.&#8217; + topObj.code + &#8216;.json.txt&#8217;

topObj.code는 서울특별시의 code인 11을 입력해 주면 된다.

```
  
[
  
{
  
code: "11110",
  
value: "종로구"
  
},
  
{
  
code: "11140",
  
value: "중구"
  
},
  
```

3) 종로구의 읍/면/동 정보를 구하기
  
다음 URL을 사용해서 json 포맷의 읍/면/동 정보를 구한다.
   
&#8216;http://www.kma.go.kr/DFSROOT/POINT/DATA/leaf.&#8217; + midObj.code + &#8216;.json.txt&#8217;

midObj.code에는 11110 을 입력해 주면 된다.

```
  
[
  
{
  
code: "1111051000",
  
value: "청운동",
  
x: "60",
  
y: "127"
  
},
  
```

4) 청운동의 날씨를 구하기
  
이제 다음 URL을 사용해서 xml 포맷의 날씨 정보를 구한다.
  
&#8216;http://www.kma.go.kr/wid/queryDFS.jsp?gridx=&#8217; + leafObj.x + &#8216;&gridy=&#8217; + leafObj.y

leafObj.x와 leafObj.y에는 각각 이전 단계에서 구한 x, y값인 60, 127을 입력해 주면 된다.

```
  
<wid>
  
<header>
  
<tm>201503051400</tm>
  
<ts>4</ts>
  
<x>60</x>
  
<y>127</y>
  
</header>
  
<body>
  
<data seq="0">
  
<hour>18</hour>
  
<day>0</day>
  
<temp>4.0</temp>
  
<tmx>-999.0</tmx>
  
<tmn>-999.0</tmn>
  
<sky>2</sky>
  
<pty>0</pty>
  
<wfKor>구름 조금</wfKor>
  
<wfEn>Partly Cloudy</wfEn>
  
<pop>10</pop>
  
<r12>0.0</r12>
  
<s12>0.0</s12>
  
<ws>3.0</ws>
  
<wd>7</wd>
  
<wdKor>북서</wdKor>
  
<wdEn>NW</wdEn>
  
<reh>25</reh>
  
<r06>0.0</r06>
  
<s06>0.0</s06>
  
</data>
  
```

wid>body에는 data의 배열이 포함되는데 seq는 예보 시간대를 의미한다.(현재 시간 이후, 0시 기준 3시간 간격, 발표 시각에 따라 총 15 ~ 18판의 data)

## 기상청 날씨 데이터 정의

각 항목에 대해 살펴보면 다음과 같다.

1) header

| 항목 | 설명                                              | 비고                                                                                              |
| -- | ----------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| tm | 발표시각:yyyymmddhhMM                               | [기상청 동네예보 RSS 정의](http://www.kma.go.kr/images/weather/lifenindustry/dongnaeforecast_rss.pdf) 참고 |
| ts | 시간 step: 동네예보 중 오늘오후(1)/내일오전(2)/내일오후(3)/모레오전(4) |                                                                                                 |
| x  | x좌표                                             |                                                                                                 |
| y  | y좌표                                             |                                                                                                 |

2) body

| 항목    | 설명                    | 비고                                                                                              |
| ----- | --------------------- | ----------------------------------------------------------------------------------------------- |
| hour  | 동네예보 3시간단위            | 18시 point값 적용요소 : 기온, 풍향, 풍속, 습도 15시~18시 구간 적용요소 : 하늘상태, 강수상태, 강수확률                             |
| day   | 오늘(0), 내일(1), 모레(2)   |                                                                                                 |
| temp  | 현재시간온도                |                                                                                                 |
| tmx   | 최고온도(값이 없을 경우 -999.0) |                                                                                                 |
| tmn   | 최저온도(값이 없을 경우 -999.0) |                                                                                                 |
| sky   | 하늘상태코드                | 1:맑음, 2:구름조금, 3:구름많음, 4:흐림                                                                      |
| pty   | 강수상태코드                | 0:없음, 1:비, 2:비/눈, 3:눈/비, 4:눈                                                                    |
| wkKor | 날씨한국어                 | 맑음, 구름 조금, 구름 많음, 흐림, 비, 눈/비, 눈                                                                 |
| wfEn  | 날씨영어                  | Clear, Partly Cloudy, Mostly Cloudy, Cloudy, Rain, Snow/Rain, Snow                              |
| pop   | 강수확률%                 |                                                                                                 |
| r12   | 12시간 예상강수량            | [기상청 동네예보 RSS 정의](http://www.kma.go.kr/images/weather/lifenindustry/dongnaeforecast_rss.pdf) 참고 |
| s12   | 12시간 예상적설량            | [기상청 동네예보 RSS 정의](http://www.kma.go.kr/images/weather/lifenindustry/dongnaeforecast_rss.pdf) 참고 |
| ws    | 풍속(m/s)               | 반올림처리                                                                                           |
| wd    | 풍향                    | 풍향(8방): 국문8방위/영문8방위, 0~7(북, 북동, 동, 남동, 남, 남서, 서, 북서)                                            |
| wdKor | 풍향한국어                 |                                                                                                 |
| wdEn  | 풍향영어                  |                                                                                                 |
| reh   | 습도%                   |                                                                                                 |
| r06   | 6시간 예상강수량             | [기상청 동네예보 RSS 정의](http://www.kma.go.kr/images/weather/lifenindustry/dongnaeforecast_rss.pdf) 참고 |
| s06   | 6시간 예상적설량             | [기상청 동네예보 RSS 정의](http://www.kma.go.kr/images/weather/lifenindustry/dongnaeforecast_rss.pdf) 참고 |

## node.js로 작성하기

전체 코드는 다음과 같다.

weather.js

```javascript
  
var http = require('http');
  
var async = require('async');
  
var querystring = require('querystring');
  
var xml2js = require('xml2js');

var http_get = function(url, data, callback) {

var query = querystring.stringify(data);
      
if (query !== '')
          
url = url + '&' + query;

//console.log(url);

http.get(url, function(res) {
              
var body = '';
              
res.setEncoding('utf8');

res.on('readable', function() {
                  
var chunk = this.read() || '';

body += chunk;
              
});

res.on('end', function() {
                  
callback(body);
              
});

res.on('error', function(e) {
                  
console.log('error', e.message);
              
});
          
});
  
};

// 기상청 API를 사용
  
// http://cloudless.tistory.com/128
  
// 한국 도시와 법정동을 사용해 X, Y 좌표 구한다.
  
// top : 도, mid : 시/군/구, leaf : 읍/면/동
  
var getKoreanWeather = function(top, mid, leaf, callback) {

async.waterfall([
          
// 도/시 검색
          
function(callback) {
              
http_get('http://www.kma.go.kr/DFSROOT/POINT/DATA/top.json.txt', {}, function(resData) {
                  
var topObj = JSON.parse(resData);
                  
for (var i = 0; i < topObj.length; ++i) {
                      
if (topObj[i].value.indexOf(top) >= 0) {
                          
callback(null, topObj[i]);
                          
return;
                      
}
                  
}
                  
callback('Can not find top', top);
              
});
          
},
          
// 시/구/군 검색
          
function(topObj, callback) {
              
http_get('http://www.kma.go.kr/DFSROOT/POINT/DATA/mdl.' + topObj.code + '.json.txt', {}, function(resData) {
                  
var midObj = JSON.parse(resData);
                  
if (mid === '') {
                      
callback(null, topObj, midObj[0]);
                      
return;
                  
} else {
                      
for (var i = 0; i < midObj.length; ++i) {
                          
if (midObj[i].value.indexOf(mid) >= 0) {
                              
callback(null, topObj, midObj[i]);
                              
return;
                          
}
                      
}
                  
}
                  
callback('Can not find mid', topObj, mid);
              
});
          
},
          
// 읍/면/동 검색
          
function(topObj, midObj, callback) {
              
http_get('http://www.kma.go.kr/DFSROOT/POINT/DATA/leaf.' + midObj.code + '.json.txt', {}, function(resData) {
                  
var leafObj = JSON.parse(resData);
                  
if (leaf === '') {
                      
callback(null, topObj, midObj, leafObj[0]);
                      
return;
                  
} else {
                      
for (var i = 0; i < leafObj.length; ++i) {
                          
if (leafObj[i].value.indexOf(leaf) >= 0) {
                              
callback(null, topObj, midObj, leafObj[i]);
                              
return;
                          
}
                      
}
                  
}
                  
callback('Can not find leaf', topObj, midObj, leaf);
              
});
          
},
          
// 날씨 검색
          
function(topObj, midObj, leafObj, callback) {
              
http_get('http://www.kma.go.kr/wid/queryDFS.jsp?gridx=' + leafObj.x + '&gridy=' + leafObj.y, {}, function(resData) {
                  
xml2js.parseString(resData, function(err, obj) {
                      
if (err) {
                          
callback(err, topObj, midObj, leafObj, null);
                      
} else {
                          
callback(null, topObj, midObj, leafObj, obj.wid.body[0].data[0]);
                      
}
                  
});
              
});
          
}
      
], function(error, topObj, midObj, leafObj, weather) {
          
callback(error, topObj, midObj, leafObj, weather);
      
});
  
};

exports.getKoreanWeather = getKoreanWeather;
  
```

## 참고

  * [기상청 RSS 인터넷 서비스](http://www.kma.go.kr/weather/lifenindustry/sevice_rss.jsp)
  * [기상청 동네예보 RSS 정의](http://www.kma.go.kr/images/weather/lifenindustry/dongnaeforecast_rss.pdf)
  * [기상청 날씨 API 사용하기](http://cloudless.tistory.com/128)