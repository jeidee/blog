---
id: 357
title: Backoff 알고리즘
date: 2015-05-13T15:48:09+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=357
permalink: '/2015/05/13/backoff-%ec%95%8c%ea%b3%a0%eb%a6%ac%ec%a6%98/'
categories:
  - java
tags:
  - backoff
  - retry
---
연결 재접속이나 데이터 재전송을 위한 Backoff 알고리즘을 java로 구현해 본다.

```java
  
<br />public class BackoffManager {
      
private int mMaxRetryCount = 10; // 최대 재시도 횟수
      
private int mInitWaitBackoffSeconds = 2; // 현재 재시도 간격(단위: 초)
      
private boolean mNoGiveupAndRepeat = false; // 최대 재시도 횟수를 초과할 경우 재시도 포기여부

private int mRetryCount;
      
private int mWaitBackoffSeconds;
      
private int mRepeatCycle; // 최대 재시도횟수를 초과한 횟수

public void init(int maxRetryCount, int initWaitBackoffSeconds, boolean noGiveupAndRepeat) {
          
mMaxRetryCount = maxRetryCount;
          
mInitWaitBackoffSeconds = initWaitBackoffSeconds;
          
mNoGiveupAndRepeat = noGiveupAndRepeat;

mRetryCount = 0;
          
mWaitBackoffSeconds = mInitWaitBackoffSeconds;
      
}

// 연결이 완료되면 백오프 리셋
      
public void reset() {
          
mRetryCount = 0;
          
mWaitBackoffSeconds = mInitWaitBackoffSeconds;
      
}

public boolean retry() throws InterruptedException {
          
if (mNoGiveupAndRepeat == false && mRepeatCycle > 0) {
              
return false;
          
}

mRetryCount++;
          
mWaitBackoffSeconds *= 2;

if (mRetryCount > mMaxRetryCount) {
              
mRetryCount = 0;
              
mWaitBackoffSeconds = mInitWaitBackoffSeconds;
              
mRepeatCycle++;
          
}

// 대기
          
Thread.sleep(mWaitBackoffSeconds * 1000);

return true;
      
}
  
}
  
```