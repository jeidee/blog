---
author: jeidee
categories:
- ios
- react-native
date: '2016-07-05'
guid: https://erlnote.wordpress.com/?p=1554
id: 1554
permalink: /2016/07/05/react-native-remote-image%ea%b0%80-%ec%b6%9c%eb%a0%a5%eb%90%98%ec%a7%80-%ec%95%8a%ec%9d%84-%ea%b2%bd%ec%9a%b0/
title: react-native remote image가 출력되지 않을 경우
url: /2016/07/05/react-native-remote-imageeab080-ecb69ceba0a5eb9098eca780-ec958aec9d84-eab2bdec9ab0
---

ios에서 info.plist를 다음과 같이 수정한다.

```
  
<key>NSAppTransportSecurity</key>
  
<dict>
  
<key>NSAllowsArbitraryLoads</key>
  
<true/>
  
</dict>
  
```

다음 링크를 참고하도록 한다.

  * https://github.com/facebook/react-native/issues/1563#issuecomment-162721129
  * https://github.com/facebook/react-native/issues/289