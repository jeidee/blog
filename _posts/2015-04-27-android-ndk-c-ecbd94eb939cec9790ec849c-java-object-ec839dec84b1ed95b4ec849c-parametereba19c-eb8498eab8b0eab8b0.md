---
id: 323
title: android ndk c++ 코드에서 java object 생성해서 Parameter로 넘기기
date: 2015-04-27T16:18:36+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=323
permalink: '/2015/04/27/android-ndk-c-%ec%bd%94%eb%93%9c%ec%97%90%ec%84%9c-java-object-%ec%83%9d%ec%84%b1%ed%95%b4%ec%84%9c-parameter%eb%a1%9c-%eb%84%98%ea%b8%b0%ea%b8%b0/'
categories:
  - android
tags:
  - ndk
---
java 클래스에 다음과 같은 staic 메소드가 있다.

```java
  
public class MsgClient {
    
&#8230;
    
public static void callbackRoster(RosterArray rosterArray) {
      
&#8230;
    
}
  
```

RosterArray는 다음과 같을 때,

```java
  
import java.util.HashMap;

public class RosterArray {
      
private HashMap<String, Roster> m_rosters;

public RosterArray() {
          
m_rosters = new HashMap<String, Roster>();
      
}

public void add(String jid, String name, int subscription) {
          
if (m_rosters.containsKey(jid)) {
              
Roster roster = m_rosters.get(jid);
              
roster.SetRoster(jid, name, subscription);
              
return;
          
}

Roster newRoster = new Roster(jid, name, subscription);
          
m_rosters.put(jid, newRoster);
      
}

&#8230;
  
}

```

c++ 코드에서 MsgClient.callbackRoster(&#8230;)를 호출할 때 다음과 같이 사용할 수 있다.

```cpp
  
void MsgClient::callbackRoster(const Roster& roster) {
      
JNIEnv *env = getJNIEnv(m_jvm);
      
if (!env) {
          
LOGE("env is null!");
          
return;
      
}

jclass rosterCls = getClassID(env, "com/jeidee/glooxforandroid/RosterArray");
      
jmethodID rosterConstructor = env->GetMethodID(rosterCls, "<init>", "()V");
      
jmethodID rosterAdd = env->GetMethodID(rosterCls, "add", "(Ljava/lang/String;Ljava/lang/String;I)V");
      
jobject rosterObj = env->NewObject(rosterCls, rosterConstructor);

for (Roster::const_iterator it = roster.begin(); it != roster.end(); ++it) {
          
jstring jid = env->NewStringUTF((*it).second->jid().c_str());
          
jstring name = env->NewStringUTF((*it).second->name().c_str());
          
int subscription = (int)(*it).second->subscription();

env->CallVoidMethod(rosterObj, rosterAdd, jid, name, subscription);

env->DeleteLocalRef(jid);
          
env->DeleteLocalRef(name);
      
}

JniMethodInfo methodInfo;
      
if (! getStaticMethodInfo(m_jvm, methodInfo, "com/jeidee/glooxforandroid/MsgClient", "callbackRoster", "(Lcom/jeidee/glooxforandroid/RosterArray;)V"))
      
{
          
return;
      
}

//jstring stringArg = methodInfo.env->NewStringUTF(value);
      
methodInfo.env->CallStaticVoidMethod(methodInfo.classID, methodInfo.methodID, rosterObj);
      
//msg.methodInfo.env->DeleteLocalRef(stringArg);
      
methodInfo.env->DeleteLocalRef(methodInfo.classID);

env->DeleteLocalRef(rosterCls);
      
env->DeleteLocalRef(rosterObj);
  
}
  
```