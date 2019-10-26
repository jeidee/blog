---
title: "(3) Flutter - 맛보기(Test Drive)"
date: 2019-10-22T20:08:00+09:00
author: jeidee
permalink: /2019/10/22/flutter-test-drive
categories:
  - flutter
tags:
  - flutter
  - ios
  - android
  - cross platform
---

> **NOTE!**  
> 이 문서는 Flutter.io의 문서를 한글로 번역한 문서입니다. [원문 바로가기](https://flutter.dev/docs/get-started/test-drive?tab=vscode)  
> 이 문서에서는 VS Code 기준으로 설명합니다. Android Studio/IntelliJ, Terminal & 다른 편집기 사용법은 원문을 참고하세요.

이 문서에서는 템플릿으로부터 새로운 Flutter App을 어떻게 만들고, 어떻게 실행하고, app에 변경을 준 후 어떻게 "핫 리로드"를 경험할 수 있는지 설명합니다.

## 새로운 App 만들기

> 웹에서 Flutter App을 실행하고 싶으신가요? Flutter의 웹 버전은 얼리 서포트 릴리즈에서 사용 가능합니다만, 몇몇의 기능이 빠져 있고 또한 아직 프로덕션 용도로 사용할 수 없는 버전입니다. 그래도 사용해 보고 싶다면 [따라해 보기](https://flutter.dev/docs/get-started/web) 문서를 참고하세요.

1. **View>Command Palette** 메뉴를 실행합니다.
2. "flutter"를 입력해서 나온 기능 리스트 중에서 **Flutter:New Project** 기능을 선택합니다.
3. 프로젝트 이름을 입력하고-예를 들어 **myapp** 과 같은- 엔터를 입력합니다.
4. 새로운 프로젝트 폴더가 만들어질 부모 디렉토리를 선택합니다.
5. 프로젝트가 생성되어 **main.dart** 파일이 보일 때까지 기다립니다.

위의 순서대로 실행하면 **myapp** 이라는 이름의 Flutter 프로젝트 디렉토리가 생성됩니다. 이 프로젝트에는 [Material Components](https://material.io/guidelines)를 사용하는 간단한 데모 앱이 포함되어 있습니다.

> **NOTE!** 새로운 Flutter App을 만들 때, 어떤 Flutter IDE 플러그인은 회사의 역순 도메인(예를 들어 com.example. 같은)을 요구하기도 합니다. 회사 도메인 이름과 프로젝트 이름은 App이 릴리즈될 때 Android(iOS에서는 번들 ID)를 위한 패키지 이름으로 함께 사용됩니다. 만약 App을 릴리즈할 계획이라면, 지금 제대로된 패키지 이름을 지정하는 것이 좋습니다. App이 한 번 릴리즈되고 나면 패키지 이름은 유니크하기 때문에 변경할 수 없습니다.

> **TIP** App을 위한 코드는 **lib/main.dart**에 있습니다. 각 코드 블럭에 대한 설명은 파일의 맨 위에 있는 주석을 참고하세요.

## App 실행

1. VS Code의 상태 바를 찾아보세요.(윈도우의 하단 파란색 바입니다.)
   ![이미지1](https://flutter.dev/assets/tools/vs-code/device_status_bar-e3e86fb35b20e3c96f9f42243dddbd538bdfecb078a7136bdfad3b103a7912bc.png)
2. **Device Selector** 영역에서 기기를 선택하세요. 자세한 내용은 [Flutter 기기간 빠른 전환](https://dartcode.org/docs/quickly-switching-between-flutter-devices)문서를 참고하세요.
   - 사용 가능한 어떤 기기도 없어 시뮬레이터를 사용하고 싶다면, **No Device**를 선택하고 시뮬레이터를 실행합니다.
   - 실제 기기를 설정하고 싶다면, [설치](https://flutter.dev/docs/get-started/install) 문서를 참고해서 OS에 맞는 적합한 기기를 설정하세요.
3. **Settings button**을 클릭하세요 - 디버그 패널에서 오른쪽 위에 디버그 텍스트 옆에 **No Configuration**이라고 적혀있는 박스 오른쪽 옆에 있는 톱니바퀴 모양의 아이콘(현재는 빨간색이나 오렌지색의 동그란 인디게이터가 표시되어 있을 겁니다.)입니다. 구성에서 **flutter**를 선택하세요. 에뮬레이터가 없다면 만들고 있다면 실행해 두세요. 실제 기기일 경우 지금 연결해 두면 됩니다.
4. **Debug>Start Debugging** 메뉴를 선택하거나 **F5**키를 누르세요.
5. App이 실행될 때까지 기다립니다. - 진행상황이 **Debug Console**에 출력됩니다.

App이 성공적으로 빌드가 완료되면, 기기에서 다음과 같은 스타터 앱을 볼 수 있습니다.

![이미지2 <>](https://flutter.dev/assets/get-started/ios/starter-app-5e284e57b8dce587ea1dfdac7da616e6ec9dc263a409a9a8f99cf836340f47b8.png)

## 핫 리로드

Flutter는 상태저장 핫 리로드(Stateful Hot Reload)를 지원해서 빠른 개발 사이클을 제공합니다. 이를 통해 재시작을 하지 않고 또한 앱의 상태를 잃지 않고 실행 중에 코드를 리로드할 수 있게 됩니다. 코드를 변경하고 IDE나 command-line 도구에서 핫 리로드를 요청하면 시뮬레이터, 에뮬레이터, 기기에서 변경된 코드 결과물을 확인할 수 있습니다.

1. **lib/main.dart**를 엽니다.
2. 다음 문자열을 수정합니다.

   > 'You have ~~pushed~~ the button this many times'

   아래와 같이

   > 'You have **clicked** the button this many times'

   > **IMPORTANT** 앱을 중지하지 마세요. 실행 중인 상태로 유지하세요.

3. 변경 사항을 저장합니다: **Save All** 메뉴를 선택하거나 **Hot Reload** 아이콘을 클릭하세요.

실행 중인 앱에서 즉시 변경된 문자열을 확인할 수 있습니다.

## 프로파일(분석) 또는 릴리즈 실행

> **IMPORTANT** 디버그 및 핫 리로드가 활성화된 상태에서 성능을 테스트하지 마세요.

지금까지 앱을 디버그 모드에서 실행했습니다. 디버그 모드는 핫 리로드 또는 단계별 디버깅등의 유용한 기능을 위해 성능 포기를 감수합니다. 디버그 모드에서는 느린 성능과 고르지 않은 애니메이션을 볼 수도 있습니다. 앱의 성능을 분석하거나 릴리즈하기 위해서는 Flutter의 "profile" 또는 "release" 빌드 모드를 선택해야 합니다. 좀 더 많은 정보는 [Flutter의 빌드 모드](https://flutter.dev/docs/testing/build-modes)문서를 참고하세요.

## 다음 단계

다음 단계에서는 작은 앱을 만들어보면서 Flutter의 핵심 컨셉을 배워보도록 합니다.
