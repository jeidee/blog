---
author: jeidee
categories:
- erlang
date: '2015-03-04'
guid: https://erlnote.wordpress.com/?p=266
id: 266
permalink: /2015/03/04/erlang%ea%b3%bc-java-%ec%97%b0%eb%8f%99/
tags:
- jinterface
title: erlang과 java 연동
url: /2015/03/04/erlangeab3bc-java-ec97b0eb8f99
---

eclipse의 maven 프로젝트 기준으로 설명한다.

## jinterface dependency 추가

먼저 pom.xml에 다음과 같이 dependency를 추가해 준다.

```
    
<dependencies>
      
<dependency>
          
<groupId>org.erlang.otp</groupId>
          
<artifactId>jinterface</artifactId>
          
<version>1.5.3.1</version>
      
</dependency>
    
</dependencies>
  
```

maven install을 수행해 jinterface-1.5.3.1.jar 를 다운로드 받는다.

## erl shell 시작

erl shell은 노드명과 매직쿠키를 지정해서 다음과 같이 실행해 준다.

```
  
erl -sname enode -setcookie erlang
  
```

또는

```
  
erl -name enode -setcookie erlang
  
```

## java를 사용해 접속 테스트

eclipse에서 ErlConnection 클래스를 추가하고 다음과 같이 작성한다.

```java
  
import com.ericsson.otp.erlang.OtpConnection;
  
import com.ericsson.otp.erlang.OtpErlangObject;
  
import com.ericsson.otp.erlang.OtpPeer;
  
import com.ericsson.otp.erlang.OtpSelf;

public class ErlConnection {

private static OtpConnection conn;
       
public OtpErlangObject received;
       
private final String peer;
       
private final String cookie;

public static void main(String []args){
           
new ErlConnection("enode","erlang");
       
}

public ErlConnection(String \_peer, String \_cookie) {
            
peer = _peer;
            
cookie = _cookie;
            
connect();

/\*Do Calls to Rpc methods and then close the connection\*/
            
disconnect();

}

private void connect() {
         
System.out.print("Please wait, connecting to "+peer+"&#8230;.n");

String javaClient ="java";
         
try {
           
OtpSelf self = new OtpSelf(javaClient, cookie.trim());
           
OtpPeer other = new OtpPeer(peer.trim());
           
conn = self.connect(other);
           
System.out.println("Connection Established with "+peer+"n");
         
}
         
catch (Exception exp) {
           
System.out.println("connection error is :" + exp.toString());
           
exp.printStackTrace();
         
}

}

public void disconnect() {
         
System.out.println("Disconnecting&#8230;.");
         
if(conn != null){
           
conn.close();
         
}
         
System.out.println("Successfuly Disconnected");
       
}

}
  
```

접속하려는 erl 노드가 동일한 네트워크에 없고 -name 으로 노드명이 지정되었을 경우 다음과 같이 코드를 수정한다. (리모트 노드의 도메인이 foo.bar라고 가정한 경우)

```java
       
public static void main(String []args){
           
new ErlConnection("enode@foo.bar","erlang");
       
}
  
```

## 참고

  * [How to communicate java and erlang](http://erlangcentral.org/wiki/index.php?title=How_to_communicate_java_and_erlang)