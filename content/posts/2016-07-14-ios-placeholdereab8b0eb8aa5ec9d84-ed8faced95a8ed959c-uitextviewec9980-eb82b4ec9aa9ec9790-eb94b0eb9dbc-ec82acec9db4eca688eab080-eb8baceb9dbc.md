---
author: jeidee
categories:
- ios
date: '2016-07-14'
guid: https://erlnote.wordpress.com/?p=1578
id: 1578
permalink: /2016/07/14/ios-placeholder%ea%b8%b0%eb%8a%a5%ec%9d%84-%ed%8f%ac%ed%95%a8%ed%95%9c-uitextview%ec%99%80-%eb%82%b4%ec%9a%a9%ec%97%90-%eb%94%b0%eb%9d%bc-%ec%82%ac%ec%9d%b4%ec%a6%88%ea%b0%80-%eb%8b%ac%eb%9d%bc/
title: iOS placeholder기능을 포함한 UITextView와 내용에 따라 사이즈가 달라지는 TableViewCell 만들기
url: /2016/07/14/ios-placeholdereab8b0eb8aa5ec9d84-ed8faced95a8ed959c-uitextviewec9980-eb82b4ec9aa9ec9790-eb94b0eb9dbc-ec82acec9db4eca688eab080-eb8baceb9dbc
---

UITextView는 UITextField에 있는 Placeholder 속성이 없다.
  
이 기능을 포함한 코드는 다음과 같다.

https://gist.github.com/jeidee/f0c62cb6410fcfe09eb49eb76e5c894a

UITableViewController를 사용해 입력 폼을 작성할 때,
  
TableViewCell에 UITextView를 추가할 경우 줄이 길어지면 해당 셀의 높이를 자동으로 조정하고 싶은 경우가 있다.

우선 UITextView를 TableViewCell의 ContentView를 기준으로 Auto Layout 설정을 했다고 가정한다.

TableViewController 클래스를 다음 코드를 참고해서 작성한다.

https://gist.github.com/jeidee/5742d62e21fbc20c3fc6b2a5cc495a35

UITextView의 속성 중에 Scrollable은 false로 되어 있어야 함을 주의한다.

주요 부분만 살펴보자면 다음과 같이 요약할 수 있다.

  * TableViewController는 UITextViewWithPlaceholderDelegate를 구현
  * tableView(&#8230;, cellForRowAtIndexPath&#8230;)에서 UITextView의 참조와 extdelegate(UITableViewWithPlaceholder 클래스에서 설정한 프로토콜)를 self로 설정
  * textViewDidChange(&#8230;)가 콜백되면 테이블의 데이터를 reload하지 않고 다시 그림
  * tableView(&#8230;, heightForRowAtIndexPath&#8230;)에서 UITextView의 내용에 맞게 Cell의 높이를 재조정