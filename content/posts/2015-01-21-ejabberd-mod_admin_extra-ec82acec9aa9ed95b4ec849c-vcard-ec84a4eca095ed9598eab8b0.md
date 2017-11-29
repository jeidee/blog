---
author: jeidee
categories:
- ejabberd
date: '2015-01-21'
guid: https://erlnote.wordpress.com/2015/01/21/ejabberd-mod_admin_extra-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-vcard-%ec%84%a4%ec%a0%95%ed%95%98%ea%b8%b0/
id: 32
permalink: /2015/01/21/ejabberd-mod_admin_extra-%ec%82%ac%ec%9a%a9%ed%95%b4%ec%84%9c-vcard-%ec%84%a4%ec%a0%95%ed%95%98%ea%b8%b0/
tags:
- vcard
title: ejabberd mod_admin_extra 사용해서 vcard 설정하기
url: /2015/01/21/ejabberd-mod_admin_extra-ec82acec9aa9ed95b4ec849c-vcard-ec84a4eca095ed9598eab8b0
---

이전 글에서 설명한 vcard 가져오기 관련된 내용 중에 수정할 부분이 있다.
  
ejabberd\_modules의 경우 최신 버전(현재 14.07)에서는 호환이 안되며 대신 ejabberd\_contrib를 사용해야 한다.
  
https://www.ejabberd.im/ejabberd-contrib

```
      
git clone git://github.com/processone/ejabberd-contrib.git
  
```

ejabberd-contrib에 포함된 mod\_admin\_extra는 ejaberd_modules에서 생기는 호환 문제가 해결되어 있으므로 수정할 필요없이 그대로 사용하면 된다.

설치(mod\_admin\_extra.erl을 ejabberd/src파일에 복사한 후 make && make install, ejabberd.yml에 mod\_admin\_extra 모듈 등록)하고 ejabberd를 재시작한 후 터미널에서 다음과 같이 테스트해 보자.

```
      
$ ejabberdctl get_vcard "romeo" "localhost" "NICKNAME"
      
hello
      
$ ejabberdctl set_vcard "romeo" "localhost" "NICKNAME" "world"
      
$ ejabberdctl get_vcard "romeo" "localhost" "NICKNAME"
      
world
  
```

닉네임이 잘 변경된다.

erl 디버그 쉘에서는 해당 모듈의 함수를 직접 호출해 테스트해보도록 하자.

```
      
$ ejabberdctl debug
      
(ejabberd@localhost)7> mod\_admin\_extra:get_vcard(<<"romeo">>, <<"localhost">>, <<"NICKNAME">>).
      
<<"world">>
  
```

get_vcard/3의 파라미터에 문자열 대신 바이너리를 입력한 이유는 다음과 같다.

```erlang
  
#ejabberd\_commands{name = get\_vcard, tags = [vcard],
         
desc = "Get content from a vCard field",
         
longdesc = Vcard1FieldsString ++ "n" ++ Vcard2FieldsString ++ "nn" ++ VcardXEP,
         
module = ?MODULE, function = get_vcard,
         
args = [{user, binary}, {host, binary}, {name, binary}],
         
result = {content, string}},
  
```

args를 보면 파라미터의 데이터타입이 모두 binary로 되어 있다.

이제 mod\_admin\_extra모듈의 get\_vcard/3과 set\_vcard/4 함수를 사용하면 vcard를 가져오거나 설정할 수 있게 된다.

코드 리뷰는 이전 글의 vcard 가져오기 내용을 참조하도록 하고 이 글은 여기에서 마무리 한다.