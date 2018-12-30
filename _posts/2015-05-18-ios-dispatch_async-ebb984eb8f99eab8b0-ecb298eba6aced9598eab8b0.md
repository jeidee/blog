---
author: jeidee
categories:
- ios
date: '2015-05-18'
guid: https://erlnote.wordpress.com/?p=366
id: 366
permalink: /2015/05/18/ios-dispatch_async-%eb%b9%84%eb%8f%99%ea%b8%b0-%ec%b2%98%eb%a6%ac%ed%95%98%ea%b8%b0/
tags:
- asynctask
- dispatch_async
- GCD
title: ios dispatch_async 비동기 처리하기
url: /2015/05/18/ios-dispatch_async-ebb984eb8f99eab8b0-ecb298eba6aced9598eab8b0
---

장시간을 요하는 작업을 UI 쓰레드에서 요청할 경우 UI 쓰레드가 블럭되기 때문에 사용자는 불편을 느끼게 된다.
  
이러한 경우, 별도의 쓰레드에서 작업을 수행 후 처리가 완료되면 UI 쓰레드로 결과를 돌려주는 방식(비동기)을 사용해야 한다.
  
android에서는 AsyncTask를 사용하면되고 ios의 경우에는 GCD(Grand Central Dispatch)를 사용하면 된다.

GCD 중에서 dispatch_async를 사용해 무거운 작업을 백그라운드 쓰레드에서 실행 후 실행완료된 결과를 UI쓰레드에서 처리하는 예제를 살펴보자.

```objc
      
mLoginView.hidden = YES;
      
mActivityIndicator.hidden = NO;
      
[mActivityIndicator startAnimating];

dispatch\_async(dispatch\_get\_global\_queue(DISPATCH\_QUEUE\_PRIORITY_DEFAULT, 0), ^{

bool success = TALK_CLIENT->connect(jid, pwd, host, port);

dispatch\_async(dispatch\_get\_main\_queue(), ^ {
              
if (success) {
                  
[self performSegueWithIdentifier:@"moveFromLoginToTabBar" sender:nil];
              
} else {
                  
[mLbError setText:@"로그인 실패"];
                  
mLoginView.hidden = NO;
                  
mActivityIndicator.hidden = YES;
              
}
          
});
      
});
  
```

dispatch\_get\_global_queue()는 커널에서 관리하는 Concurrent Dispatch Queue이며, 백그라운드에서 작업을 수행하고 큐에 저장된 작업을 병행 처리한다.

dispatch\_get\_main_queue()는 UI 쓰레드를 관리하는 메인 큐로 Serial Dispatch Queue로서 큐에 저장된 작업을 순차적으로 처리하게 된다.

## 참고

  * [GCD(Grand Central Dispatch) 사용하기](http://padgom.tistory.com/entry/iOS-%EA%B8%B0%EB%B3%B8-GCDGrand-Central-Dispatch-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
  * [iOS 특정 코드를 비동기로 실행시키기](http://seorenn.blogspot.kr/2012/04/ios.html)