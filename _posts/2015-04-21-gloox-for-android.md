---
author: jeidee
categories:
- android
- ejabberd
date: '2015-04-21'
guid: https://erlnote.wordpress.com/?p=292
id: 292
permalink: /2015/04/21/gloox-for-android/
tags:
- gloox
- ndk
- openssl
title: gloox for android
url: /2015/04/21/gloox-for-android
---

gloox를 android에서 사용하기 위한 과정을 살펴보자.

## 사전 준비 작업

다음과 같은 환경이 구축되어 있다고 가정한다.

  * 개발 환경 : Mac OS X Yosemite 10.10.3
  * Android Studio : 1.1.0 
      * Android SDK 경로는 ~/Android/sdk로 변경
  * NDK 
      * ~/Android/ndk에 복사하고 path 지정

## Android Studio에 ndk 설정

Preference > External Tools에서 + 버튼을 클릭해 javah, ndk-build, ndk-build clean 도구를 추가한다.

1) javah

![javah_설정](http://localhost:8888/wordpress/wp-content/uploads/2015/04/ec8aa4ed81aceba6b0ec83b7-2015-04-21-ec98a4eca084-11-14-17.png)

2) ndk-build

![ndk-build](http://localhost:8888/wordpress/wp-content/uploads/2015/04/ec8aa4ed81aceba6b0ec83b7-2015-04-21-ec98a4eca084-11-15-40.png)

3) ndk-build clean

![ndk-build clean](http://localhost:8888/wordpress/wp-content/uploads/2015/04/ec8aa4ed81aceba6b0ec83b7-2015-04-21-ec98a4eca084-11-16-40.png)

## Android Project 생성후 NDK 테스트

1) GlooxForAndroid 이름을 갖는 Android Project를 생성한다.

2) app/src/main/res/layout/activity_main.xml을 다음과 같이 수정한다.

```
  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      
xmlns:tools="http://schemas.android.com/tools" android:layout\_width="match\_parent"
      
android:layout\_height="match\_parent" android:paddingLeft="@dimen/activity\_horizontal\_margin"
      
android:paddingRight="@dimen/activity\_horizontal\_margin"
      
android:paddingTop="@dimen/activity\_vertical\_margin"
      
android:paddingBottom="@dimen/activity\_vertical\_margin" tools:context=".MainActivity">

<Button
          
android:id="@+id/callBtn"
          
android:layout\_width="wrap\_content"
          
android:layout\_height="wrap\_content"
          
android:text="Call JNI" />

<TextView
          
android:id="@+id/textVal"
          
android:layout\_width="wrap\_content"
          
android:layout\_height="wrap\_content"
          
android:textSize="15pt" />
  
</LinearLayout>
  
```

Button 하나와 TextView 하나를 추가했다.

3) MainActivity.java를 다음과 같이 수정한다.

```java
  
public class MainActivity extends ActionBarActivity {

private Button btn_;
      
private TextView textView_;

@Override
      
protected void onCreate(Bundle savedInstanceState) {
          
super.onCreate(savedInstanceState);
          
setContentView(R.layout.activity_main);

btn_ = (Button) findViewById(R.id.callBtn);
          
textView_ = (TextView) findViewById(R.id.textVal);

btn_.setOnClickListener(new View.OnClickListener() {
              
@Override
              
public void onClick(View v) {
                  
NativeCall nativeCall = new NativeCall();
                  
int ret = nativeCall.add(10, 20);
                  
String retStr = nativeCall.stringFromJNI();
                  
textView_.setText(retStr);
              
}
          
});
      
}
  
```

btn\_을 클릭했을 때 NativeCall클래스의 함수를 호출한 결과를 textView\_에 출력한다.
  
NativeCall클래스는 jni를 사용해 c/c++ 코드를 호출하게 된다.

4) NativeCall.java 클래스를 생성한다.

```java
  
public class NativeCall {
      
static {
          
System.loadLibrary("my_lib");
      
}

public native int add(int i, int j);

public native String stringFromJNI();
  
}
  
```

Project 탐색기의 NativeCalll.java 클래스에서 마우스 우측 버튼으로 팝업메뉴를 출력한 후 NDK>javah를 선택하면,
  
jni 디렉토리에 com\_example\_jeidee\_glooxforandroid\_NativerCall.h파일이 생성된다.

5) my_lib.cpp 파일을 생성한다.

```cpp
  
#include "com\_example\_jeidee\_ndktest\_NativeCall.h"

JNIEXPORT jint JNICALL Java\_com\_jeidee\_glooxforandroid\_NativeCall_add
    
(JNIEnv *, jobject, jint i, jint j) {
      
return i + j;
    
}

JNIEXPORT jstring JNICALL Java\_com\_jeidee\_glooxforandroid\_NativeCall_stringFromJNI
    
(JNIEnv *env, jobject) {
      
return env->NewStringUTF("Hello JNI!!!!!");
    
}

```

6) ndk-build

app/src/build.gradle 파일을 열고 다음과 같이 ndk와 sourceSets.main 항목을 입력한다.

```json
  
android {
      
compileSdkVersion 22
      
buildToolsVersion "21.1.2"

defaultConfig {
          
applicationId "com.jeidee.glooxforandroid"
          
minSdkVersion 15
          
targetSdkVersion 22
          
versionCode 1
          
versionName "1.0"

ndk {
              
moduleName "my_lib"
          
}

sourceSets.main {
              
jniLibs.srcDir 'src/main/libs'
              
jni.srcDirs = [] //important!!!! disable automatic ndk-build call
          
}
      
}

```

Project 탐색기의 NativeCall.java 클래스에서 마우스 우측 버튼으로 팝업메뉴를 출력한 후 NDK ndk-build를 선택하면,
  
빌드가 완료되고 src/main/libs/armeabi 디렉토리에 libmy_lib.so 공유 라이브러리 파일이 생성된다.

7) 빌드 후 테스트

안드로이드 디바이스를 연결후 Run &#8216;app&#8217;을 실행한다.

## openssl for android 빌드

gloox는 openssl 라이브러리를 사용하기 때문에 openssl 소스를 빌드해 라이브러리를 얻어야 한다.
  
android를 위해 누군가 [github에 ndk-build를 위한 구성과 소스](https://github.com/eighthave/openssl-android)를 올려 놓았으므로 다운로드 받아 사용하도록 한다.

git clone한 후 해당 디렉토리에서 ndk-build를 수행하면 된다.

성공적으로 빌드가 완료되면 libs/armeabi 디렉토리에 libssl.so와 libcrypto.so 파일이 생성된다.
  
해당 파일을 Android Project의 src/main/jni 하위에 libs 디렉토리를 생성하고 이 곳에 복사해 넣는다.

src/main/jni 하위에 openssl 디렉토리를 생성하고,
  
openssl의 include와 crypto 디렉토리를 복사해 넣는다.

## gloox for xcode 에서 소스 파일 복사해 오기

이 글을 작성하는 현시점에서 gloox는 1.0.13버전이 최신버전이다.
  
해당 버전의 소스를 받아 ./configure &#8211;host=arm-linux를 수행한 후 소스파일을 복사해 사용해도 되지만,
  
[gloox for xcode]() 오픈소스를 받아 해당 프로젝트에 포함된 gloox 파일을 사용하도록 한다.

git clone한 후 Utils/gloox 디렉토리를 app/src/main/jni 디렉토리에 복사해 넣는다.

## Android.mk 수정

1) openssl for android 빌드 결과물인 libcrypto와 libssl 라이브러리를 다음과 같이 Android.mk파일의 맨 위해 추가한다.

```
  
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE := libcrypto

LOCAL\_SRC\_FILES := libs/libcrypto.so

include $(PREBUILT\_SHARED\_LIBRARY)

include $(CLEAR_VARS)

LOCAL_MODULE := libssl

LOCAL\_SRC\_FILES := libs/libssl.so

include $(PREBUILT\_SHARED\_LIBRARY)
  
```

2) gloox의 소스파일을 다음과 같이 추가한다.

```
  
LOCAL\_SRC\_FILES :=
                      
my_lib.cpp
                      
gloox/adhoc.cpp
                      
gloox/amp.cpp
                      
gloox/annotations.cpp
                      
gloox/attention.cpp
                      
gloox/base64.cpp
                      
gloox/bookmarkstorage.cpp
                      
gloox/capabilities.cpp
                      
gloox/chatstate.cpp
                      
gloox/chatstatefilter.cpp
                      
gloox/client.cpp
                      
gloox/clientbase.cpp
                      
gloox/component.cpp
                      
gloox/compressiondefault.cpp
                      
gloox/compressionzlib.cpp
                      
gloox/connectionbosh.cpp
                      
gloox/connectionhttpproxy.cpp
                      
gloox/connectionsocks5proxy.cpp
                      
gloox/connectiontcpbase.cpp
                      
gloox/connectiontcpclient.cpp
                      
gloox/connectiontcpserver.cpp
                      
gloox/connectiontls.cpp
                      
gloox/connectiontlsserver.cpp
                      
gloox/dataform.cpp
                      
gloox/dataformfield.cpp
                      
gloox/dataformfieldcontainer.cpp
                      
gloox/dataformitem.cpp
                      
gloox/dataformreported.cpp
                      
gloox/delayeddelivery.cpp
                      
gloox/disco.cpp
                      
gloox/dns.cpp
                      
gloox/error.cpp
                      
gloox/eventdispatcher.cpp
                      
gloox/featureneg.cpp
                      
gloox/flexoff.cpp
                      
gloox/gloox.cpp
                      
gloox/gpgencrypted.cpp
                      
gloox/gpgsigned.cpp
                      
gloox/inbandbytestream.cpp
                      
gloox/instantmucroom.cpp
                      
gloox/iq.cpp
                      
gloox/jid.cpp
                      
gloox/lastactivity.cpp
                      
gloox/logsink.cpp
                      
gloox/md5.cpp
                      
gloox/message.cpp
                      
gloox/messageevent.cpp
                      
gloox/messageeventfilter.cpp
                      
gloox/messagefilter.cpp
                      
gloox/messagesession.cpp
                      
gloox/mucmessagesession.cpp
                      
gloox/mucroom.cpp
                      
gloox/mutex.cpp
                      
gloox/nickname.cpp
                      
gloox/nonsaslauth.cpp
                      
gloox/oob.cpp
                      
gloox/parser.cpp
                      
gloox/prep.cpp
                      
gloox/presence.cpp
                      
gloox/privacyitem.cpp
                      
gloox/privacymanager.cpp
                      
gloox/privatexml.cpp
                      
gloox/pubsubevent.cpp
                      
gloox/pubsubitem.cpp
                      
gloox/pubsubmanager.cpp
                      
gloox/receipt.cpp
                      
gloox/registration.cpp
                      
gloox/rosteritem.cpp
                      
gloox/rostermanager.cpp
                      
gloox/search.cpp
                      
gloox/sha.cpp
                      
gloox/shim.cpp
                      
gloox/simanager.cpp
                      
gloox/siprofileft.cpp
                      
gloox/socks5bytestream.cpp
                      
gloox/socks5bytestreammanager.cpp
                      
gloox/socks5bytestreamserver.cpp
                      
gloox/softwareversion.cpp
                      
gloox/stanza.cpp
                      
gloox/stanzaextensionfactory.cpp
                      
gloox/subscription.cpp
                      
gloox/tag.cpp
                      
gloox/tlsdefault.cpp
                      
gloox/tlsgnutlsbase.cpp
                      
gloox/tlsgnutlsclient.cpp
                      
gloox/tlsgnutlsclientanon.cpp
                      
gloox/tlsgnutlsserveranon.cpp
                      
gloox/tlsopensslbase.cpp
                      
gloox/tlsopensslclient.cpp
                      
gloox/tlsopensslserver.cpp
                      
gloox/tlsschannel.cpp
                      
gloox/uniquemucroom.cpp
                      
gloox/util.cpp
                      
gloox/vcard.cpp
                      
gloox/vcardmanager.cpp
                      
gloox/vcardupdate.cpp
                      
gloox/xhtmlim.cpp
  
```

3) openssl의 header files 경로를 다음과 같이 추가한다.

```
  
LOCAL\_C\_INCLUDES := $(LOCAL_PATH)/openssl/include/
  
LOCAL\_C\_INCLUDES += $(LOCAL_PATH)/openssl/
  
```

4) openssl shared library와 zlib를 링크하기 위해 다음과 같이 추가하고 마무리한다.

```
  
LOCAL\_SHARED\_LIBRARIES := libssl libcrypto

LOCAL_LDLIBS := -llog
  
LOCAL_LDLIBS += -lz

include $(BUILD\_SHARED\_LIBRARY)
  
```

LOCAL_LDLIBS += -lz 설정을 통해 zlib이 링크된다.

## Application.mk 생성

c++ stl library 사용을 위해 jni 디렉토리 밑에 Application.mk파일을 생성하고 다음과 같이 입력한다.

```
  
APP\_STL := c++\_static
  
```

## ndk-build

1) error: &#8216;atoi&#8217; was not declared in this scope

.cpp 상단에 #include 를 추가해 준다.

2) RSA\_generate\_key() deprecate error

NativeCall.java 파일을 ndk-build하면 tlsopensslserver.cpp 컴파일 도중 에러가 발생하는데, RSA\_generate\_key()가 deprecate 되었기 때문에 다음 소스 코드를 추가해야 한다.
  
(참고 : [with openssl porting guide](http://aucd29.tistory.com/m/post/1101))

```c
  
RSA *RSA\_generate\_key(int bits, unsigned long e_value,
           
void (\*callback)(int,int,void \*), void *cb_arg)
      
{
      
BN_GENCB cb;
      
int i;
      
RSA *rsa = RSA_new();
      
BIGNUM *e = BN_new();

if(!rsa || !e) goto err;

/* The problem is when building with 8, 16, or 32 BN_ULONG,
       
\* unsigned long can be larger \*/
      
for (i=0; i<(int)sizeof(unsigned long)*8; i++)
          
{
          
if (e_value & (1UL<<i))
              
if (BN\_set\_bit(e,i) == 0)
                  
goto err;
          
}

BN\_GENCB\_set\_old(&cb, callback, cb\_arg);

if(RSA\_generate\_key_ex(rsa, bits, e, &cb)) {
          
BN_free(e);
          
return rsa;
      
}
  
err:
      
if(e) BN_free(e);
      
if(rsa) RSA_free(rsa);
      
return 0;
      
}
  
```

## 전체 프로젝트 다운로드

github에 올려 놓았다.

https://github.com/jeidee/GlooxForAndroid

## 참고

  * [openssl-android](https://github.com/eighthave/openssl-android)
  * [gloox for android](http://herry.sinaapp.com/?p=31)
  * [Setting Up an ARM EABI Toolchain on Mac OS X](http://blog.y3xz.com/blog/2012/10/07/setting-up-an-arm-eabi-toolchain-on-mac-os-x)
  * [Android에서 OpenSSL의 암호 알고리즘 사용하기 &#8211; 1편](http://asos.tistory.com/13)
  * [Android Project 에서 OpenSSL 사용하기](http://choizak.tistory.com/63)