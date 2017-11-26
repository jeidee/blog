---
id: 354
title: c++ GetTickCount() on linux
date: 2015-05-13T13:01:24+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=354
permalink: /2015/05/13/c-gettickcount-on-linux/
categories:
  - c++
tags:
  - tick
  - time
---
gettimeofday()는 향후 제거될 함수이며 권장되지 않기 때문에 clock_gettime()을 사용한 방법을 사용하도록 한다.
  
특히 경과시간을 구하고자할 때 현재 시간을 반환하는 gettimeofday()함수는 시각 조정에 의한 오차가 발생할 수 있고 쓰레드에 안전하지 않으므로,
  
clock\_gettime()에 CLOCK\_MONOTONIC(uptime) 옵션을 사용해서 사용하는 것이 좋다.
  
clock_gettime()은 쓰레드에 안전하다.

```cpp
  
#include <unistd.h>
  
#include <time.h>

uint32_t getTick() {
      
struct timespec ts;
      
unsigned theTick = 0U;
      
clock\_gettime( CLOCK\_MONOTONIC, &ts );
      
theTick = ts.tv_nsec / 1000000;
      
theTick += ts.tv_sec * 1000;
      
return theTick;
  
}
  
```

osx에서는 clock_gettime()을 사용할 수 없으므로 다음과 같이 코드한다.

```cpp
  
#include "Util.h"

#include <unistd.h>
  
#include <time.h>

#ifdef \_\_MACH\_\_
  
#include <mach/clock.h>
  
#include <mach/mach.h>
  
#endif

namespace jd {
      
uint32_t Util::getTick() {
          
struct timespec ts;
          
unsigned theTick = 0U;

#ifdef \_\_MACH\_\_ // OS X does not have clock\_gettime, use clock\_get_time
          
clock\_serv\_t cclock;
          
mach\_timespec\_t mts;
          
host\_get\_clock\_service(mach\_host\_self(), CALENDAR\_CLOCK, &cclock);
          
clock\_get\_time(cclock, &mts);
          
mach\_port\_deallocate(mach\_task\_self(), cclock);
          
ts.tv\_sec = mts.tv\_sec;
          
ts.tv\_nsec = mts.tv\_nsec;
  
#else
          
clock\_gettime( CLOCK\_MONOTONIC, &ts );
  
#endif

theTick = ts.tv_nsec / 1000000;
          
theTick += ts.tv_sec * 1000;
          
return theTick;
      
}
  
}
  
```

## 참고

  * [current\_utc\_time](https://gist.github.com/jbenet/1087739)
  * [gettimeofday를 대체하는 clock_gettime 함수](http://sunyzero.tistory.com/161)
  * [Equivalent to GetTickCount() on Linux](http://stackoverflow.com/questions/2958291/equivalent-to-gettickcount-on-linux)