---
title: "unity3d Main Camera 설정하기"
date: 2015-04-09T10:07:39+09:00
author: jeidee
categories:
- game programming
tags:
- unity3d
---

Main Camera를 제거하고 새로 Camera를 추가한 경우 Camera.main 값은 null이 되는 경우가 있다.
새로 추가한 Camera를 Main Camera로 설정하고자 할 경우 Inspector에서 Tag를 "MainCamera"로 변경하면 된다.