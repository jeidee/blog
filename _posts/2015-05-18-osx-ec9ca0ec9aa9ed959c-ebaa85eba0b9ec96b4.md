---
id: 375
title: osx 유용한 명령어
date: 2015-05-18T20:32:44+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=375
permalink: '/2015/05/18/osx-%ec%9c%a0%ec%9a%a9%ed%95%9c-%eb%aa%85%eb%a0%b9%ec%96%b4/'
categories:
  - utility
tags:
  - osx
---
Dock Dealy 없애기

```
  
defaults write com.apple.Dock autohide-delay -float 0 && killall Dock
  
```

미션 컨트롤 화면 전환 속도를 보다 빠르게

```
  
defaults write com.apple.dock expose-animation-duration -float 0.12 && killall Dock
  
```

숨겨진 APP 아이콘 DOCK에서 쉽게 구별 하기

```
  
defaults write com.apple.Dock showhidden -bool YES && killall Dock
  
```

OSX 메일에서 EMAIL 주소 대신 이름으로 가리는 문제 해결 하기

```
  
defaults write com.apple.mail AddressesIncludeNameOnPasteboard -bool false
  
```

미리보기에서 문자열 선택 가능 하기

```
  
defaults write com.apple.finder QLEnableTextSelection -bool TRUE;killall Finder
  
```

항상 숨김 파일 표시하기

```
  
defaults write com.apple.finder AppleShowAllFiles -bool YES && killall Finder
  
```

바탕화면 아이콘 모두 숨기기

```
  
defaults write com.apple.finder CreateDesktop -bool false && killall Finder
  
```

로그인 화면에서 시스템 정보 보기

```
  
sudo defaults write /Library/Preferences/com.apple.loginwindow AdminHostInfo HostName
  
```

기본 캡쳐 이미지 저장 장소 바꾸기

```
  
defaults write com.apple.screencapture location ~/Pictures/Screenshots
  
```

기본 캡쳐 이미지 종류 바꾸기

```
  
defaults write com.apple.screencapture type jpg && killall SystemUIServer
  
```

항상 유저 라이브러리 폴더 보이기
  
&#8220;\`
  
chflags nohidden ~/Library/