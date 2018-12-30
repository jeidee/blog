---
author: jeidee
categories:
- erlang
date: '2015-02-14'
guid: https://erlnote.wordpress.com/?p=175
id: 175
permalink: /2015/02/14/mnesia-%ea%b8%b0%ec%b4%88-44-getting-started-with-mnesia-22/
tags:
- mnesia
title: mnesia 기초 4/4 &#8211; Getting Started with Mnesia 2/2
url: /2015/02/14/mnesia-eab8b0ecb488-44-getting-started-with-mnesia-22
---

## 데이터 추가

테이블에 데이터를 추가하기 위해서는 트랜잭션을 사용해야 한다. 트랜잭션은 매개변수가 없는 익명의 fun에 여러 DB 오퍼레이션을 묶어 생성할 수 있다.

테이블에 데이터를 입력할 때는 mnesia:write/1 함수를 사용할 수 있으면 매개변수는 레코드를 사용한다.

```erlang
      
insert_emp(Emp, DeptId, ProjNames) ->
          
Ename = Emp#employee.name,
          
Fun = fun() ->
                        
mnesia:write(Emp),
                        
AtDep = #at\_dep{emp = Ename, dept\_id = DeptId},
                        
mnesia:write(AtDep),
                        
mk_projs(Ename, ProjNames)
                
end,
          
mnesia:transaction(Fun).

mk_projs(Ename, [ProjName|Tail]) ->
          
mnesia:write(#in\_proj{emp = Ename, proj\_name = ProjName}),
          
mk_projs(Ename, Tail);
      
mk\_projs(\_, []) -> ok.
  
```

insert_emp/3함수의 첫번째 매개변수는 employ레코드 타입이어야 하며, ProjNames는 리스트여야 한다.

DeptId는 어떤 타입이든 상관 없지만 아마도 문자열이면 될 것이다.

재밌는 것은 mk_projs/2함수인데, ProjNames리스트를 [H|T]로 나누어서 마지막 요소가 더이상 없을때(빈 리스트인 [])까지 재귀호출을 하는 루틴을 갖는다.

결국 트랜잭션은 하나의 employee write와 하나의 at\_dep write와 여러 개의 in\_proj write로 구성된다.

## 데이터 검색

mnesia에서 데이터를 검색하는 방법은 match spec을 사용하는 방법과 qlc를 사용하는 방법이 있다.

다음과 같이 employee 테이블에 데이터를 입력해 보자.

```erlang
        
F = fun() ->
          
mnesia:write({employee, 104465, "Johnson Torbjorn", 1, male, 99184, {242,038}}),
          
mnesia:write({employee, 107912, "Carlsson Tuula", 2, female,94556, {242,056}}),
          
mnesia:write({employee, 114872, "Dacker Bjarne", 3, male, 99415, {221,035}}),
          
mnesia:write({employee, 104531, "Nilsson Hans", 3, male, 99495, {222,026}}),
          
mnesia:write({employee, 104659, "Tornkvist Torbjorn", 2, male, 99514, {222,022}}),
          
mnesia:write({employee, 104732, "Wikstrom Claes", 2, male, 99586, {221,015}}),
          
mnesia:write({employee, 117716, "Fedoriw Anna", 1, female,99143, {221,031}}),
          
mnesia:write({employee, 115018, "Mattsson Hakan", 3, male, 99251, {203,348}})
        
end,
        
mnesia:transaction(F).
  
```

## key 사용

employee 테이블에서 emp_no가 104465인 레코드를 검색해 보자.

다음과 같이 mnesia:read/2 또는 mnesia:dirty_read/2를 사용해 검색할 수 있다.

```erlang
      
7> mnesia:dirty_read(employee, 104465).
      
[{employee,104465,"Johnson Torbjorn",1,male,99184,{242,38}}]
  
```

## match spec

키 이외의 값으로 조회하고자 한다면 어떻게 해야 할까?
  
mnesia:select/2 또는 mnesia:dirty_select/2 함수를 사용하면 된다.

```erlang
      
15> rr(company).
      
16> mnesia:dirty\_select(employee, [{#employee{sex = female, name = '$1', \_ = '_'},[], ['$1']}]).
      
["Carlsson Tuula","Fedoriw Anna"]
  
```

employee 레코드 사용을 위해 15번 라인에서 rr() 명령을 사용했다. erlang shell에서 레코드 정의 파일(company.hrl)을 로드할 때 rr() 명령을 사용할 수 있다.

mnesia:dirty_xxx 함수는 트랜잭션이 필요없는 함수로 트랜잭션이 완료되지 않은 값(트랜잭션 실패로 취소될 수 있는 값)을 읽을때 사용한다.

16번 라인이 의미하는 바를 sql query로 바꾸면 다음과 같다.(Match Spec에 관련한 내용은 이전 글을 참고)

```erlang
      
select name from employee where sex = 'female';
  
```

모든 컬럼을 쿼리하려면 다음과 같이 하면 된다.

```erlang
      
17> mnesia:dirty\_select(employee, [{#employee{sex = female, \_ = '\_'},[], ['$\_']}]).
      
[#employee{emp_no = 107912,name = "Carlsson Tuula",
                 
salary = 2,sex = female,phone = 94556,
                 
room_no = {242,56}},
       
#employee{emp_no = 117716,name = "Fedoriw Anna",salary = 1,
                 
sex = female,phone = 99143,
                 
room_no = {221,31}}]
  
```

## qlc 사용

QLC 쿼리는 match spec과 함께 mnesia 함수를 사용하는 것보다 더 고비용이지만 깔끔한 구문을 사용할 수 있게 해 준다.

```erlang
  
females() ->
    
F = fun() ->
      
Q = qlc:q([E#employee.name || E <- mnesia:table(employee),
        
E#employee.sex == female]),
      
qlc:e(Q)
    
end,
    
mnesia:transaction(F).
  
```

qlc구문은 리스트해석과 가드를 조합한 모양이다.

```erlang
  
qlc:q([E#employee.name || E <- mnesia:table(employee),
        
E#employee.sex == female]),
  
```

이 부분의 코드를 처리 순서대로 분석해 보면 다음과 같다.

  1. mnesia:table(employee) : employee의 모든 데이터를 리스트로 반환
  2. , E#employee.sex == female : sex가 female인 데이터만 필터링
  3. E#employee.name : 필터링된 데이터에서 name field만 리스트로 출력

위와 같은 처리 순서로 보았을 때, sex에 인덱스가 설정되어 있다고해도 mnesia 함수와는 달리 full scan은 피할 수 없어 보인다.