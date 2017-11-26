---
id: 35
title: ejabberd jabber search 수정하기
date: 2015-01-21T23:56:15+00:00
author: jeidee
layout: post
guid: 'https://erlnote.wordpress.com/2015/01/21/ejabberd-jabber-search-%ec%88%98%ec%a0%95%ed%95%98%ea%b8%b0/'
permalink: '/2015/01/21/ejabberd-jabber-search-%ec%88%98%ec%a0%95%ed%95%98%ea%b8%b0/'
categories:
  - ejabberd
tags:
  - jabber search
  - mysql
---
jabber search를 다음과 같이 수정해야 한다고 가정해 본다.

  * like query 가능해야 함
  * 페이징 가능해야 함

우선 페이징이 가능하도록 검색폼과 결과폼을 다음과 같이 수정해야 한다.

  * 검색폼 : page no, page size 필드 추가
  * 결과폼 : 현재 page no, 현재 page size(result count), 결과가 더 있는지 여부(more result) 필드 추가

## 검색폼 수정

검색폼을 수정하기 위해 ejabberd의 모듈 중에 mod_vcard.erl을 다음과 같이 수정하면 된다.

```erlang
  
-define(FORM(JID),
               
?TLFIELD(<<"text-single">>, <<"Organization Unit">>,
                    
<<"orgunit">>),
               
?TLFIELD(<<"text-single">>, <<"Page Number">>,
                    
<<"pageno">>),
               
?TLFIELD(<<"text-single">>, <<"Page Size">>,
                    
<<"pagesize">>)]}]).
  
```

?FORM 매크로의 마지막 부분에 pageno와 pagesize 필드를 추가한다.
  
FORM은 클라이언트에서 submit해야 하는 데이터폼을 정의한다.

## 결과폼 수정

결과폼을 수정하기 위해서 다음과 같이 수정한다.

```erlang
  
search_result(Lang, JID, ServerHost, Data) ->
      
?DEBUG("============ search_result, Data = ~p~n", [Data]),
      
SearchResult = search(ServerHost, Data),
      
[TPageNo] = proplists:get_value(<>, Data),
      
[TPageSize] = proplists:get_value(<>, Data),
      
PageNo = binary\_to\_integer(TPageNo),
      
PageSize = binary\_to\_integer(TPageSize),
      
ResultSize = length(SearchResult),
      
More = (PageNo * PageSize
      
%%?DEBUG("========== search(~p, ~p)~n", [LServer, Data]),
      
[TNickname] = proplists:get_value(<>, Data),
      
Nickname =
          
case str:suffix(<>, TNickname) of
              
true ->
                  
str:substr(TNickname, 1, byte_size(TNickname) &#8211; 1);
              
_ -> TNickname
          
end,
      
[TPageNo] = proplists:get_value(<>, Data),
      
[TPageSize] = proplists:get_value(<>, Data),
      
PageNo = binary\_to\_integer(TPageNo),
      
PageSize = binary\_to\_integer(TPageSize),
      
Result = search(LServer, MatchSpec, AllowReturnAll, PageNo, PageSize, DBType),
      
Result.
  
```

search/4함수를 PageNo, PageSize 매개변수를 추가해서 search/6함수가 호출되도록 수정하였다.

위 코드의 변경사항은 페이징을 위해 출력할 페이지번호와 페이지당 출력할 건수 정보를 검색폼에서 가져와 search/6함수에 넘겨주는 내용이다.

기존의 search/4함수에 PageNo와 PageSize를 추가해서 다음과 같이 search/6함수로 변경한다.

```erlang
  
search(LServer, MatchSpec, AllowReturnAll, PageNo, PageSize, odbc) ->
      
if (MatchSpec == <>) and not AllowReturnAll -> [];
         
true ->
         
Limit = case gen\_mod:get\_module_opt(LServer, ?MODULE, matches,
                                                 
fun(infinity) -> infinity;
                                                    
(I) when is_integer(I),
                                                             
I>0 ->
                                                         
I
                                                 
end, ?JUD_MATCHES) of
                         
infinity ->
                             
<>;
                         
Val ->
                             
[<>,
                              
jlib:integer\_to\_binary(Val)]
                     
end,
       
?DEBUG("=========== Limit is ~p", [Limit]),
         
case catch ejabberd\_odbc:sql\_query(LServer,
                            
[<>,
                             
MatchSpec, [<>,list\_to\_binary(io_lib:format("~p,~p", [PageNo * PageSize, PageSize]))], <>])
             
of
           
{selected,
            
[<>, <>, <>, <>,
             
<>, <>, <>, <>,
             
<>, <>, <>,
             
<>],
            
Rs}
           
when is_list(Rs) ->
           
Rs;
           
Error -> ?ERROR_MSG("~p", [Error]), []
         
end
      
end;
  
```

search(~, odbc) 함수 외에도 search(~, mnesia)와 search(~, riak)함수의 매개변수를 동일하게 수정해야 한다.
  
위 코드에서 페이징과 관련한 부분은 Limit 부분이다.

페이징을 위해 다음과 같은 코드를 사용한다.

```erlang
  
[<>,list\_to\_binary(io_lib:format("~p,~p", [PageNo * PageSize, PageSize]))], <>])
  
```

주의할 것은, 위의 페이징을 위한 코드는 mysql에서만 동작한다는 것이다.