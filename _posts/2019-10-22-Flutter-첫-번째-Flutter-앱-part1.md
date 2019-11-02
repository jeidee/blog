---
title: "(4) Flutter - 첫 번째 Flutter App 만들기, part1"
date: 2019-10-22T22:08:00+09:00
author: jeidee
permalink: /2019/10/22/flutter-첫-번째-Flutter-앱-part1
categories:
  - flutter
tags:
  - flutter
  - ios
  - android
  - cross platform
---

> **NOTE!**  
> 이 문서는 Flutter.io의 문서를 한글로 번역한 문서입니다. [원문 바로가기](https://flutter.dev/docs/get-started/codelab)

## 목차

[Step1: 스타터 Flutter App 만들기](#Step1:-스타터-Flutter-App-만들기)  
[Step2: 외부 패키지 사용](#Step2:-외부-패키지-사용)  
[Step3: 상태기반 위젯 만들기](#Step3:-상태기반-위제-만들기)  
[Step4: 무한 스크롤 리스트뷰 만들기](#Step4:-무한-스크롤-리스트뷰-만들기)  
[프로파일 또는 릴리즈 실행](프로파일-또는-릴리즈-실행)

이 문서는 첫 번째 Flutter App을 만드는데 도움을 주는 안내서입니다. 만약 객체지향코드나 변수, 반복문, 조건문 등의 기본 프로그래밍 컨셉에 익숙하다면 이 튜토리얼을 완료할 수 있습니다. Dart나 모바일/웹 프로그래밍에 대한 경험이 없어도 됩니다.

이 문서는 두 개의 파트로 구성된 코드랩(codelab)의 첫 번째 파트(part 1)입니다. [두 번째 파트](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2)는 [Google Developer](https://codelabs.developers.google.com/)에서 찾을 수 있습니다. [첫 번째 파트](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1) 역시 [Google Developer](https://codelabs.developers.google.com/)에서 찾을 수 있습니다.

![이미지1](https://flutter.dev/assets/get-started/startup-namer-part-1-9db323d8383da0000c8be4e1a12e3d9ff6ab3a0eb8b86984451b329f1f3b4196.gif)

## Part 1에서 만드는 것

스타트업 회사의 이름을 제안하는 간단한 모바일 앱을 만들어 봅니다. 사용자는 이름을 선택하거나 선택해제할 수 있고 그 중 하나를 저장할 수 있습니다. 코드는 이름을 지연하여(lazily) 생성합니다. 즉, 사용자가 스크롤할 때 더 많은 이름을 생성하게 됩니다. 사용자는 스크롤을 제한 없이 할 수 있습니다.

위 애니메이션 GIF는 part 1에서 완성된 앱이 어떻게 동작하는지 보여줍니다.

> ### part 1에서 배울 수 있는 것들
>
> - iOS, Android, Web에서 자연스럽게 보이는 Flutter App을 만드는 방법
> - Flutter App의 기본 구조
> - 기능 확장을 위해 패키지를 찾고 사용하는 방법
> - 빠른 개발 사이클을 위해 핫 리로드 하는 방법
> - 상태 저장 위젯을 구현하는 방법
> - 무한 지연 로드 리스트를 생성하는 방법
>
> 코드실습(codelab)의 [part 2](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2)에서는 대화형 기능을 추가하고, App의 테마를 수정하고, 새로운 화면으로 이동하는 기능(Flutter의 라우터라 불리우는)을 추가합니다.

> ### 사용하는 것들
>
> 이 실습을 완료하려면 두 개의 소프트웨어가 필요합니다. [Flutter SDK](https://flutter.dev/docs/get-started/install)와 [편집기](https://flutter.dev/docs/get-started/editor)입니다. 이 코드 실습에서는 Android Studio를 사용한다고 가정하지만 선호하는 다른 편집기를 사용해도 됩니다.
>
> 다음 기기에서 이 코드 실습을 실행해 볼 수 있습니다:
>
> - 개발자 모드로 설정되어 있고 컴퓨터에 연결된 물리 기기([Android](https://flutter.dev/docs/get-started/install/macos#set-up-your-android-device) 또는 [iOS](https://flutter.dev/docs/get-started/install/macos#deploy-to-ios-devices))
> - [iOS 시뮬레이터](https://flutter.dev/docs/get-started/install/macos#set-up-the-ios-simulator)
> - [Android 에뮬레이터](https://flutter.dev/docs/get-started/install/macos#set-up-the-android-emulator)
> - 웹 브라우저(Chrome을 선호합니다)

## Step 1: Starter Flutter App 만들기

[첫 번째 Flutter App 만들기](https://jeidee.github.io/2019/10/19/flutter-%EC%84%A4%EC%B9%98#%EA%B0%84%EB%8B%A8%ED%95%9C-flutter-app%EC%9D%84-%EB%A7%8C%EB%93%A4%EA%B3%A0-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0) 순서에 따라 간단한 템플릿화된 Flutter App을 만듭니다. 프로젝트 이름은 myapp 대신 **startup_namer**로 입력합니다.

> **TIP** IDE에서 "New Flutter Project" 메뉴가 보이지 않는 다면, [Flutter와 Dart를 위한 플러그인 설치](https://flutter.dev/docs/get-started/editor)문서를 참고하세요.

이번 코드 실습에서는 Dart 코드가 있는 **lib/main.dart** 파일을 대부분 편집하게 됩니다.

1. **lib/main.dart**파일의 내용을 교체합니다.
   **lib/main.dart** 파일의 내용을 모두 삭제후 화면의 중앙에 "Hello World" 문자열을 출력하는 아래 코드로 바꿉니다.

   ```dart
   // Copyright 2018 The Flutter team. All rights reserved.
   // Use of this source code is governed by a BSD-style license that can be
   // found in the LICENSE file.

   import 'package:flutter/material.dart';

   void main() => runApp(MyApp());

   class MyApp extends StatelessWidget {
     @override
     Widget build(BuildContext context) {
       return MaterialApp(
         title: 'Welcome to Flutter',
         home: Scaffold(
           appBar: AppBar(
             title: Text('Welcome to Flutter'),
           ),
           body: Center(
             child: Text('Hello World'),
           ),
         ),
       );
     }
   }
   ```

   > **TIP** 코드를 붙여넣을 때 들여쓰기가 이상해질 수 있습니다. Flutter 도구를 사용해서 자동적으로 수정되도록 할 수 있습니다.
   >
   > - Android Studio / IntelliJ IDEA: 마우스 우측 버튼을 클릭 후 **Reformat Code with dartfmt**를 선택합니다.
   > - VS Code: 마우스 우측 버튼을 클릭한 후 **Format Document**를 선택합니다.
   > - Terminal: **flutter format \<filename\>**을 실행합니다.

2. [각자의 IDE](https://flutter.dev/docs/get-started/test-drive)에서 App을 실행합니다. Android나 iOS에서 각자의 기기에 따라 다음과 같은 화면을 보게 됩니다.

   ![이미지1](https://flutter.dev/assets/get-started/android/hello-world-fadb9765c01d33f0fea92d7ac767e036ea90e9159335ea1841277bc6bef3a10a.png)
   ![이미지2](https://flutter.dev/assets/get-started/ios/hello-world-ed7cf47213953bfca5eaa74fba63a78538d782f2c63a7c575068f3c2f7298bde.png)

   > **TIP** 물리기기에서 처음 실행할 때, 로드하는데 다소 시간이 소요될 수 있습니다. 이후에는 핫 리로드를 사용해서 빠르게 갱신할 수 있습니다. App이 실행 중일 때 **저장**하면 핫 리로드가 수행됩니다.

### 살펴보기

- 이 예제는 Material App을 만듭니다. [Material](https://material.io/guidelines)은 모바일과 웹에서 표준인 시각적인 디자인 언어(Visual Design Language)입니다. Flutter는 풍부한 Material 위젯을 제공합니다.
- **main()** 메서드는 화살표(**=>**) 표기법을 사용할 수 있습니다. 화살표 표기법은 한 줄의 함수나 메서드에 사용됩니다.
- App은 **StatelessWidget**을 확장하여 App 자체를 위젯으로 만듭니다. Flutter에서는 대부분이 모두 정렬과 패딩, 레이아웃을 포함하는 위젯입니다.
- Material 라이브러리의 **Scaffold** 위젯은 홈 화면에서 위젯 트리를 구성하는 기본적인 app bar, title, body 속성을 제공합니다.
- 위젯의 주요 임무는 다른 하위 레벨 위젯으로 위젯을 표시하는 방법을 설명하는 **build()** 함수를 제공하는 것입니다.
- 이 예제의 body는 **Text** 자식 위젯을 포함하는 **Center** 위젯으로 구성됩니다. Center 위젯은 서브트리로 구성된 자식 위젯들을 화면의 중앙에 배치합니다.

## Step 2: 외부 패키지 사용

이번 단계에서는, 오픈 소스 패키지 중 하나인 [english_words](https://pub.dev/packages/english_words) 패키지를 사용합니다. 이 패키지에는 수천개의 자주 사용되는 영어 단어들과 유용한 함수가 포함되어 있습니다.

[pub.dev](https://pub.dev/)에서 **english_words** 패키지 뿐만 아니라 더 많은 오픈 소스 패키지들을 찾을 수 있습니다.

1. **pubspec** 파일은 Flutter App에서 의존성 관리를 담당합니다. **pubspec.yaml** 파일을 열어 **english_words**(3.1.0 이상) 패키지를 다음과 같이 추가합니다:

   ```yaml
   dependencies:
     flutter:
       sdk: flutter
     cupertino_icons: ^0.1.2
     english_words: ^3.1.0
   ```

2. Android Studio의 편집 뷰에서 pubspec을 보는 동안 **Packages get**을 클릭하면 프로젝트에 의존하는 패키지들을 가져오게 됩니다. 콘솔에서는 다음과 같이 실행할 수 있습니다.

   ```sh
   $ flutter pub get
   Running "flutter pub get" in startup_namer...
   Process finished with exit code 0
   ```

   **Packages get**을 실행하면 프로젝트로 가져오는 모든 패키지 리스트와 버전이 **pubspec.lock** 파일에 자동 생성됩니다.

3. **lib/main.dart**에는 해당 패키지를 import하는 코드를 작성합니다.

   ```dart
   import 'package:flutter/material.dart';
   import 'package:english_words/english_words.dart';
   ```

   Android Studio에서 타이핑하면 import할 라이브러리를 자동으로 제안하고 해당 문자열을 회색으로 렌더링해서 아직 사용되고 있지 않음을 보여줍니다.

4. **english_words** 패키지를 사용해서 "Hello World" 대신에 자동 생성된 단어들을 출력합니다:

   ```dart
   class MyApp extends StatelessWidget {
   @override
   Widget build(BuildContext context) {
    final wordPair = WordPair.random();
    return MaterialApp(
      title: 'Welcome to Flutter',
      home: Scaffold(
      ...
        title: Text('Welcome to Flutter'),
        ),
        body: Center(
          // child: Text('Hello World'),
          child: Text(wordPair.asPascalCase),
        ),
      ),
    );
   ```

   > **NOTE** "Pascal case"(Camel case로 알려진)는, 문자열에서 각 단어의 첫 글자가 대문자로 시작하는 방식을 의미합니다. 그래서 "uppercamelcase"는 "UpperCamelCase"가 됩니다.

5. App이 실행 중인 경우, 실행 중인 App을 갱신하기 위해 [핫 리로드](https://flutter.dev/docs/get-started/test-drive)를 사용하세요. 핫 리로드(hot reload)를 클릭할 때마다, 또는 프로젝트를 저장할 때마다, 실행 중인 App에서 랜덤으로 선택된 다른 단어 쌍을 볼 수 있습니다. 이렇게 되는 이유는 MaterialApp이 렌더링될 때마다(또는 Flutter Inspector에서 Platform을 변경할 때마다) 실행되는 **build** 메서드때문이며, 이 메서드 안에서 단어 쌍을 매번 새로 생성하기 때문입니다.

   ![이미지1](https://flutter.dev/assets/get-started/android/step2-45e5a16e59926599e09b25b1c6d37372f77b69151cbebd070d2f94f657b1abde.png)
   ![이미지2](https://flutter.dev/assets/get-started/ios/step2-de9da90d2a90351c8d651c50f345c822472dbcd93cd8bad099e4edf2aa499c43.png)

### 문제가 있나요?

App이 기대한대로 실행되지 않는다면, 오타를 확인해 보세요. Flutter의 디버깅 도구를 사용하고 싶다면, [DevTools](https://flutter.dev/docs/development/tools/devtools) 에서 디버깅과 프로파일링 도구를 확인하면 됩니다. 필요하다면, 다음 링크의 코드를 사용해서 본인의 코드와 비교해 보세요.

- [pubspec.yaml](https://raw.githubusercontent.com/flutter/codelabs/master/startup_namer/step2_use_package/pubspec.yaml)
- [lib/main.dart](https://raw.githubusercontent.com/flutter/codelabs/master/startup_namer/step2_use_package/lib/main.dart)

## Step 3: 상태 저장 위젯(Stateful widget) 추가하기

상태 비저장 위젯(Stateless widget)은 변경될 수 없으므로(속성이 변경될 수 없음을 의미) 모든 값은 최종값(**final**)입니다.

상태 저장 위젯은 위젯의 생명주기동안 변경되는 상태를 유지관리합니다. 상태 저장 위젯을 구현하기 위해서는 적어도 다음 두 클래스가 필요합니다: 1) StatefulWidget 클래스는 2) State 클래스의 인스턴스를 생성합니다. StatefulWidget 클래스는 그 자체는 불변하지만 State 클래스는 위젯의 생명주기동안 유지됩니다.

이번 단계에서, **RandomWordsState** 상태(**State**) 클래스를 생성하는 **RandomWords** 상태 저장 위젯을 추가하게 됩니다. 그런 다음 기존의 **MyApp** 상태 비저장 위젯의 자식으로 **RandomWords**를 사용하도록 작성합니다.

1. 최소 상태 클래스를 생성합니다. **main.dart**의 하단에 다음 코드를 추가합니다:

   ```dart
   class RandomWordsState extends State<RandomWords> {
     // TODO Add build() method
   }
   ```

   **State\<RandomWords\>** 선언을 주목해 보죠. 이 것이 가리키는 것은 **RandomWords** 클래스를 사용하도록 특수화된 일반화(generic) **State** 클래스를 사용한다는 것을 의미합니다. 대부분의 App 로직(logic)과 상태는 여기에 있고 **RandomWords** 위젯의 상태를 유지합니다. 이 클래스는 사용자가 스크롤을 할 때마다 생성된 단어 쌍과 [part 2](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2)에서 구현할 **즐겨찾기**(하트 아이콘을 토글할 때 마다 추가하거나 삭제하게 되는)한 단어 쌍을 저장합니다.

   **RandomWordsState**는 **RandomWords**클래스에 의존적입니다. 다음에 추가합니다.

2. **main.dart**에 상태 저장 **RandomWords** 위젯을 추가합니다. **RandomWords** 위젯은 State 클래스를 생성하는 것 외에는 거의 하는 일이 없습니다:

   ```dart
   class RandomWords extends StatefulWidget {
     @override
     RandomWordsState createState() => RandomWordsState();
   }
   ```

   State 클래스를 추가한 이후에, IDE는 클래스에 build 메서드가 없다고 출력합니다. 다음에는 단어 생성 코드를 **MyApp**에서 **RandomWordsState** 클래스로 이동해서 단어 쌍을 생성하는 build 매서드를 추가합니다.

3. **RandomWordsState** 클래스에 **build()** 메서드를 추가합니다:

   ```dart
   class RandomWordsState extends State<RandomWords> {
     @override
     Widget build(BuildContext context) {
       final wordPair = WordPair.random();
       return Text(wordPair.asPascalCase);
       }
     }
   ```

4. 아래 코드와 같이 **MyApp**에서 단어 생성 코드를 제거합니다:

   ```dart
   class MyApp extends StatelessWidget {
     @override
     Widget build(BuildContext context) {
   -  final wordPair = WordPair.random();
      return MaterialApp(
        title: 'Welcome to Flutter',
        home: Scaffold(
   @@ -18,8 +17,8 @@
             title: Text('Welcome to Flutter'),
           ),
           body: Center(
   -        child: Text(wordPair.asPascalCase),
   +        child: RandomWords(),
           ),
         ),
       );
     }
   ```

5. App을 재시작합니다. App은 이전과 같이 작동하며, 핫 리로드를 하거나 App을 저장할 때마다 새로운 단어 쌍을 출력합니다.

> **TIP** 핫 리로드를 할 때 다음과 같은 경고가 출력될 경우, App을 재시작하세요:
>
> **Reloading…**  
> **Some program elements were changed during reload but did not run when the view was reassembled; you might need to restart the app (by pressing “R”) for the changes to have an effect.**
>
> 이 경우는 오탐지일 수 있는데, 재시작하므로써 App의 UI에 변경사항이 정확히 반영되도록 할 수 있습니다.

### 문제가 있나요?

App이 정확히 실행되지 않을 경우, 오타를 확인하세요. Flutter의 디버깅 도구를 사용하고 싶다면, [DevTools](https://flutter.dev/docs/development/tools/devtools) 에서 디버깅과 프로파일링 도구를 확인하면 됩니다. 필요하다면, 다음 링크의 코드를 사용해서 본인의 코드와 비교해 보세요.

- [lib/main.dart](https://raw.githubusercontent.com/flutter/codelabs/master/startup_namer/step4_infinite_list/lib/main.dart)

## 프로파일 또는 릴리즈 실행

> **IMPORTANT** 디버그나 핫리로드가 활성화된 상태에서 App의 성능을 테스트하지 마세요.

지금까지 디버그 모드에서 앱을 실행했습니다. 디버그 모드는 핫 리로드 및 단계별 디버깅과 같은 유용한 개발자 기능을 위해 성능 저하를 감수합니다. 디버그 모드에서 성능이 저하되고 고르지 않은 애니메이션이 나타나는 것은 예상 가능합니다. 일단 App의 성능을 분석하거나 릴리즈할 준비가 되면, Flutter의 "profile"이나 "release" 빌드 모드를 사용해야 합니다. 좀 더 자세한 내용은 [Flutter의 빌드 모드](https://flutter.dev/docs/testing/build-modes)문서를 참고하세요.

## 다음 단계

축하합니다!

이제 iOS와 Android에서 실행되는 상호작용하는 Flutter App을 만들었습니다. 이번 코드 실습에서 진행된 내용은 다음과 같습니다:

- 밑바닥부터 Flutter App을 만들었습니다.
- Dart 코드를 작성했습니다.
- 외부의 써드파티 라이브러리를 사용했습니다.
- 빠른 개발 사이클을 위해 핫 리로드를 사용했습니다.
- 상태 저장 위젯을 구현했습니다.
- 지연 로딩을 작성했고 무한 스크롤 리스트를 구현했습니다.

이 App을 확장하고 싶다면, [Google Developers Codelabs](https://codelabs.developers.google.com/) 사이트에 있는 [part 2](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2)를 따라서 다음의 기능을 추가할 수 있습니다:

- 즐겨찾기 단어 쌍을 저장하기 위해 클릭 가능한 Heart 아이콘을 추가해서 상호작용하는 코드를 구현합니다.
- 저장된 즐겨찾기를 출력하는 새로운 화면을 추가해서 새로운 경로로 이동할 수 있는 네비게이션 기능을 추가합니다.
- 색상 테마를 수정해서 모든 것이 하얀 App을 만듭니다.

![이미지3](https://flutter.dev/assets/get-started/startup-namer-b85740ef920a57e28524f84e9c06cfb380128d10780e91eaf2c7613a56f3f511.gif)
