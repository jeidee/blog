---
id: 371
title: ios c++ 객체에서 objective-c 객체의 함수 콜백하기
date: 2015-05-18T17:08:39+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=371
permalink: '/2015/05/18/ios-c-%ea%b0%9d%ec%b2%b4%ec%97%90%ec%84%9c-objective-c-%ea%b0%9d%ec%b2%b4%ec%9d%98-%ed%95%a8%ec%88%98-%ec%bd%9c%eb%b0%b1%ed%95%98%ea%b8%b0/'
categories:
  - ios
tags:
  - callback
  - delegate
  - protocol
---
다음과 같은 요구사항이 있다고 가정해 보자.

> c++로 작성된 MsgClient는 통신 처리를 하는 별도의 워커쓰레드가 있다.
    
> 서버에 접속한 후 데이터를 수신하면 objective-c의 여러 ViewHandler에 데이터 수신 이벤트를 발생시켜야 한다.
    
> 이벤트 수신을 원하는 ViewHandler가 이벤트 수신을 받은 후 UI 쓰레드에서 여러 UI 컨트롤을 처리해야 한다. 

간단히 말하면 서로 다른 쓰레드에서 동작하는 c++ 오브젝트와 objective-c 오브젝트가 있고, c++ 오브젝트에서 objective-c 오브젝트의 특정 함수를 호출(콜백)할 수 있어야 한다.

위와 같은 요구사항을 처리하기 위해서 다양한 방법이 있겠지만 다음과 같은 구조를 사용해 해결해 보도록 하자.
  
(아래에 나오는 코드는 의사코드임을 감안하도록 한다.)

## c++과 objective-c간 인터페이스 정의

```cpp
  
class TalkClientEventHandler {
  
public:
      
TalkClientEventHandler();
      
virtual ~TalkClientEventHandler();

virtual void onConnect();
      
virtual void onDisconnect(int e);
  
}; // class ITalkClientEventHandler
  
```

TalkClientEventHandler는 objective-c 클래스에서 구현하고 c++ 클래스에서 사용하게 될 인터페이스이다.

## c++ TalkClient

```cpp
  
#include "TalkClientEventHandler.h"

class TalkClient : public MsgClient {
  
public:
      
virtual void handleConnect();
      
virtual void handleDisconnect(int e);

private:
      
TalkClientEventHandler* mTalkClientEventHandler;
  
};

void TalkClient::handleConnect() {
      
mTalkClientEventHandler->onConnect();
  
}

void TalkClient::handleDisconnect() {
      
mTalkClientEventHandler->onDisconnect();
  
}

```

MsgClient 클래스는 메세지의 네트워크 통신 처리를 하는 클래스이고 별도의 워커쓰레드에서 데이터를 수신하면 handleXXX() 콜백함수를 호출하게 된다.
  
TalkClient는 MsgClient를 상속하고 handleXXX() 콜백함수가 호출되면 TalkClientEventHandler를 구현한 객체의 onXXXX() 함수를 콜백한다.

## objective-c TalkInterface

이 클래스가 제일 중요한데 c++과 objective-c의 인터페이스 역할을 수행하도록 작성해야 한다.
  
핵심은 TalkClientEventHandler 클래스의 구현부를 objective-c의 .mm에 작성하는 것이다.
  
TalkInterface.mm 파일을 보면 알 수 있듯이, objective-c 코드를 사용할 수 있게 된다.

TalkInterface.h

```objc
  
#import "TalkClientEventHandler.h"

@protocol TalkClientDelegate
  
@optional
  
&#8211; (void) onConnect;
  
&#8211; (void) onDisconnect:(int) e;
  
@end

@interface TalkInterface : NSObject
  
{
      
NSMutableArray* mEventHandlerList;
  
}

+ (TalkInterface*) getInstance;
  
&#8211; (id) init;
  
&#8211; (void) addEventHandler:(id )eventHandler;
  
&#8211; (void) removeEventHandler:(id )eventHandler;
  
@end
  
```

TalkInterface.mm

```objc
  
#import "TalkInterface.h"

@implementation TalkInterface

TalkClientEventHandler::TalkClientEventHandler() {
  
}

TalkClientEventHandler::~TalkClientEventHandler() {
  
}

// TalkInterface는 objective-c의 클래스이지만 c++ 코드인 TalkClientEventHandler에서 사용할 수 있게 된다.
  
void TalkClientEventHandler::onConnect() {
      
[[TalkInterface getInstance] onConnect];
  
}

void TalkClientEventHandler::onDisconnect(int e) {
      
[[TalkInterface getInstance] onDisconnect:e];
  
}

+(TalkInterface*)getInstance {
      
static dispatch\_once\_t pred;
      
static TalkInterface* instance = nil;

dispatch_once(&pred, ^{
          
instance = [[TalkInterface alloc] init];
      
});

return instance;
  
}

-(id)init {
      
self = [super init];
      
if (self) {
          
mEventHandlerList = [[NSMutableArray alloc] init];
      
}
      
return self;
  
}

-(void)addEventHandler:(id )eventHandler {
      
[mEventHandlerList addObject:eventHandler];
  
}

-(void)removeEventHandler:(id )eventHandler {
      
for (id eh in mEventHandlerList) {
          
if ([eh isEqual:eventHandler]) {
              
[mEventHandlerList removeObject:eventHandler];
              
return;
          
}
      
}
  
}

/*
   
*
   
*/
  
-(void)onConnect {
      
dispatch\_async(dispatch\_get\_main\_queue(), ^ {
          
for (id eventHandler in mEventHandlerList) {
              
// delegate 함수가 @optional일 경우 사용가능한지 확인이 필요하다.
              
if ([eventHandler respondsToSelector:@selector(onConnect)])
                  
[eventHandler onConnect];
          
}
      
});
  
}

-(void)onDisconnect:(int)e {
      
dispatch\_async(dispatch\_get\_main\_queue(), ^ {
          
for (id eventHandler in mEventHandlerList) {
              
if ([eventHandler respondsToSelector:@selector(onDisconnect:)])
                  
[eventHandler onDisconnect:e];
          
}
      
});
  
}

@end
  
```

## objective-c LoginViewController UI 클래스에서 delegate를 등록하고 이벤트 수신하기

LoginViewController.h

```objc
  
#import "TalkInterface.h"

@interface LoginViewController : UIViewController
  
```

TalkClientDelegate를 구현해야 한다.

LogtinViewController.mm

```objc
  
&#8211; (void) viewWillDisappear:(BOOL)animated {
      
[[TalkInterface getInstance] removeEventHandler:self];
  
}

&#8211; (void) viewWillAppear:(BOOL)animated {
      
[[TalkInterface getInstance] addEventHandler:self];
      
mActivityIndicator.hidden = YES;
  
}

-(void)onConnect {
      
NSLog(@"LoginViewController onConnect");
  
}
  
```

view가 출력될 때 TalkInterface에 이벤트핸들러를 등록하고 view가 사라질 때 TalkInterface에서 이벤트핸들러를 제거한다.
  
c++ 객체인 TalkClient 객체에서 handleConnect()가 콜백되면 TalkInterface를 통해 최종 LoginViewController의 onConnect() 함수가 호출되게 된다.
  
눈여겨 볼 것은 TalkInterface에서 dispatch\_async(dispatch\_get\_main\_queue(), &#8230;)를 통해 UI 쓰레드에 비동기로 함수 콜백하게 되는 부분이다.

## 참고

  * [The Basics of Protocols and Delegates](http://iosdevelopertips.com/objective-c/the-basics-of-protocols-and-delegates.html)
  * [not implemented delegate method leads to crash](http://stackoverflow.com/questions/9018764/not-implemented-delegate-method-leads-to-crash)