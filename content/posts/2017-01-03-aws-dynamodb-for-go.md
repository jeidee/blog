---
author: jeidee
categories:
- aws
- go
date: '2017-01-03'
guid: https://erlnote.wordpress.com/?p=1667
id: 1667
permalink: /2017/01/03/aws-dynamodb-for-go/
tags:
- DynamoDB
title: AWS DynamoDB for Go
url: /2017/01/03/aws-dynamodb-for-go
---

AWS의 Go언어를 위한 DynamoDB SDK를 알아본다.

## credentials를 사용한 DynamoDB 세션과 DB객체 생성하기

우선 AWS에서 Credentials Key, Secret을 생성 후 개발서버에 저장한다.

일반적으로 ~/.aws/credentials 파일에 다음과 같은 구조로 저장한 후 사용하게 되는데, 다른 경로에 설정한 경우에는 해당 파일의 경로와 프로파일을 지정해 사용할 수 있다.

```
  
[default]
  
aws\_access\_key_id = xxx
  
aws\_secret\_access_key = xxx
  
```

위와 같이 Credentials 파일을 생성한 후 DynamoDB에 접근할 수 있는 세션과 객체를 생성하는 코드는 다음과 같다.

https://gist.github.com/jeidee/4bb85ae4bd5cdefcee60bc4e4879eb6b

GetDynamoDB() 함수에는 최대 2개의 가변 파라미터를 입력할 수 있는데,
  
다음과 같이 사용할 수 있다.

  1. GetDynamoDB(&#8220;aws_region&#8221;)
  2. GetDynamoDB(&#8220;aws\_region&#8221;, &#8220;credentials\_file_path:profile&#8221;)

1번과 같이 사용할 경우 ~/.aws/credentials파일을 사용하게 되며 기본 프로파일은 default를 사용한다.
  
2번과 같이 사용할 경우 지정된 경로의 credetials파일을 사용하게 되며 프로파일까지 지정했을 경우 지정된 프로파일을 사용한다.

## 테이블 생성

테이블을 생성하는 코드는 다음과 같이 작성한다.

https://gist.github.com/jeidee/3f53abebb407b40149230340e28490ed

DynamoDB는 스키마가 없는 NoSQL이지만 MongoDB의 Collection이나 Couchbase의 Bucket처럼 데이터를 저장하는 테이블을 생성해 줘야 한다.
  
또한 테이블을 생성할 때는 Primary Key와 인덱스로 사용할 속성을 지정해 주어야 하며 Primary Key와 인덱스는 키 스키마를 정의해야 한다.
  
PK와 인덱스에서 사용되지 않는 속성은 지정할 수 없다.
  
키 스키마는 파티션키(HASH)와 정렬키(RANGE) 두 개의 키 타입을 지정할 수 있으며, 파티션키(HASH)는 필수로 지정해야 한다.

### 보조 인덱스(Secondary Index)

DynamoDB는 GSI(Global Secondary Index)와 LSI(Local Secondary Index)를 지원한다.
  
둘의 차이는 다음과 같다.

  * LSI : 테이블과 파티션키가 동일하고 정렬키는 다른 인덱스
  * GSI : 테이블과 파티션키, 정렬키가 모두 다른 인덱스

PK와 마찬가지로 인덱스는 처리량이 제한되어 있고 비용에 직결하므로 신중하게 생성해야 한다.

### Projection

Projection은 인덱스를 생성할 때 복사하는 테이블의 속성값 들을 지정한다.
  
다음과 같이 3개의 타입이 있다.

  * ALL : 모든 속성을 복사한다.
  * KEYS_ONLY : 오직 인덱스와 PK를 구성하는 속성만 복사한다.
  * INCLUDE : 특별히 지정한 속성만 복사한다.

## GetItem

https://gist.github.com/jeidee/34d3f0a571e4f642a1e7a565a616afbf

GetItem() 함수의 경우 지정된 테이블의 PK값을 입력해 데이터를 조회할 수 있다.
  
GetItemInput 객체에 다음과 같은 값을 설정해서 매개변수로 전달한다.

  * ConsistentRead : 일관성 있는 값을 조회해야 할 때 사용한다. true일 경우 강한 일관성(strongly consistent read)읽기를 사용하며 false일 경우 일관성 없는(eventually consistent read)읽기를 시도한다.</p> 
  * eventually consistent read : 분산환경에서 특정 노드에서 수정된 데이터가 다른 노드에서는 수정되기 전의 데이터를 읽을 수 있는 경우</p> 

## Query

https://gist.github.com/jeidee/1461e027be5520d43b6e1c4f466acb5c

Query() 함수의 경우 지정된 테이블의 PK이외의 인덱스를 사용해서 데이터를 질의할 수 있다.
  
QueryInput 객체에 다음과 같은 값을 설정해서 매개변수로 전달한다.

  * IndexName: Query에 사용할 인덱스명을 지정한다.
  * ConsistentRead : 인덱스가 GSI인 경우 항상 eventually consistent read만 가능하며 ConsistentRead가 true일 경우 ValidationException이 발생한다.
  * ScanIndexForward: true일 경우 오름차순, false일 경우 내림차순 정렬
  * KeyConditionExpression: 검색 조건식을 입력할 수 있다. 자세한 내용은 문서 참고
  * ExpressionAttributeValues: 조건식에서 값으로 사용할 변수를 map 자료구조로 지정한다.
  * ExpressionAttributeNames: 조건식에서 Status, Name 같은 키워드를 쓰고자할 때 #변수명과 같은 형식으로 map 자료구조에 저장해 지정할 수 있다.

## PutItem

https://gist.github.com/jeidee/1bc41fce48d93624746f2aa99ca7160a

데이터를 생성한다.
  
없으면 생성하고 있을 경우 기존 속성을 수정한다. 지정되지 않은 속성은 삭제한다.

  * Item: Key또는 인덱스로 지정된 속성은 필수로 입력되어야 하며, 기타 속성은 자유로이 추가할 수 있다.
  * ConditionExpression: 데이터를 생성할 때 체크해야할 조건을 지정할 수 있다.

## UpdateItem

https://gist.github.com/jeidee/ecd955049abb255cdaf024bb039a6453

데이터를 수정한다.
  
없으면 생성하고 있을 경우 수정한다. 지정되지 않은 속성은 그대로 둔다.
  
PutItem과의 차이는 지정되지 않은 속성을 처리하는 방식이다.

  * Key: 수정할 데이터의 PK
  * UpdateExpression: SET XXX = :xxx와 같은 업데이트 표현식
  * ConditionExpression: 조건식. 조건을 만족할 경우 Update 수행
  * ReturnValues
  
    ** NONE: 업데이트후 값을 반환하지 않는다.
  
    ** ALL_OLD: 업데이트전 데이터를 반환한다.
  
    ** UPDATED_OLD: 업데이트된 속성의 이전값만 반환한다.
  
    ** ALL_NEW: 업데이트후 모든 새로운 데이터를 반환한다.
  
    ** UPDATED_NEW: 업데이트된 속성의 새로운값만 반환한다.

## DeleteItem

특이사항이 없어 생략한다.