---
title: "(5) Flutter - 첫 번째 Flutter App 만들기, part2"
date: 2019-10-26T22:10:00+09:00
author: jeidee
permalink: /2019/10/26/flutter-첫-번째-Flutter-앱-part2
categories:
  - flutter
tags:
  - flutter
  - ios
  - android
  - cross platform
---

> **NOTE!**  
> 이 문서는 Flutter.io의 문서를 한글로 번역한 문서입니다. [원문 바로가기](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/#0)

## 소개

Flutter는 iOS와 Android에서 고 수준의 Native 인터페이스를 만들 수 있는 구글의 모바일 SDK입니다. Flutter는 전 세계의 개발자와 조직에서 사용되는 기존 코드와 함께 작동하고, 무료이며 오픈 소스입니다.

이번 코드 실습에서는, 상호작용 기능이 포함된 기본적인 Flutter App을 확장해 볼 예정입니다. 두 번째 페이지(경로라고 불리는)를 만들고 사용자는 해당 페이지로 이동할 수 있습니다. 마지막으로 App의 컬러 테마를 변경합니다. 이 코드 실습은 [part 1](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1/)을 확장하지만 part 2에서 바로 시작하기를 원하는 사용자를 위해 시작 코드를 제공합니다.

### part2에서 배우는 것들

- iOS와 Android에서 자연스럽게 보이는 Flutter App 만들기
- 빠른 개발 사이클을 위해 핫 리로드 사용하기
- 상태 저장 위젯에 상호작용 기능을 추가하는 방법
- 두 번째 화면을 만들고 이동하는 방법
- App의 테마를 변경하는 방법

### part2에서 만드는 것

스타트업 회사를 위해 끝없이 이름을 생성해 주는 간단한 모바일 앱을 만드는 것으로 시작합니다. 이번 코드 실습 이후에, 사용자는 이름을 선택하거나 선택해제하면서 최상의 이름을 찾아 저장할 수 있습니다. 우측 상단의 리스트 아이콘을 탭하면 즐겨찾기된 이름 리스트가 있는 새로운 페이지로 이동합니다.

아래 GIT는 완성된 App의 작동 방식을 보여줍니다.

![이미지1](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/b17de15fa7831a1c.gif)

## Flutter 환경 설정

part1을 완료하지 않았다면 [part1](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1/) 문서에 있는 [Flutter 환셩 설정](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1/#1) 부분을 참고해서 Flutter 개발환경을 설정합니다.

## 시작 App 구하기

part1을 수행해서 이미 **startup_namer**이름을 갖는 시작 App을 만든 경우 다음 단계를 수행합니다.
**startup_namer** App이 없을 경우 다음 안내를 따라 주세요.

1. [첫 번째 Flutter App 만들기](https://flutter.io/get-started/test-drive/#create-app)문서를 보고 간단한 Flutter App을 만드세요. 프로젝트 이름은 **startup_namer**로 합니다.
2. **lib/main.dart** 파일의 내용을 모두 삭제하세요. 스타트업 이름을 제안하는 지연된 로딩 기능과 무한 출력 기능을 구현한 [이 파일](https://github.com/flutter/codelabs/blob/b3293b5bb0c0187bdbe8112f7759f4d75f4c040a/startup_namer/step4_infinite_list/lib/main.dart)의 내용으로 다시 작성합니다.
3. English Words 패키지를 추가하기 위해서 **pubspec.yaml** 파일을 다음과 같이 수정하세요.

```yaml
dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^0.1.2
  english_words: ^3.1.0    // Add this line.
```

English Words 패키지는 랜덤하게 단어 쌍을 생성해서 스타트업 이름을 제안해 줍니다.

4. Android Studio의 편집 뷰에서 pubspec을 보는 동안 우측 상단의 **Packages get**을 클릭합니다. 콘솔에서 다음과 같은 내용을 볼 수 있습니다:

```
flutter packages get
Running "flutter packages get" in startup_namer...
Process finished with exit code 0
```

5. app을 실행합니다. 원하는 만큼 스크롤을 하면 스타트업 이름이 계속해서 생성되어 출력되는 것을 볼 수 있습니다.

## 리스트에 아이콘 추가하기

이번 단계에서, 각 로우에 하트 아이콘을 추가합니다. 그 다음에는 해당 아이콘을 탭해서 즐겨찾기할 수 있는 코드를 작성합니다.

1. RandomWordsState클래스에 **\_saved** Set을 추가합니다. 이 Set에는 사용자가 즐겨찾기한 단어 쌍을 저장합니다. Set은 중복된 요소를 허용하지 않으므로 List보다 낫습니다.

```dart
class RandomWordsState extends State<RandomWords> {
  final List<WordPair> _suggestions = <WordPair>[];
  final Set<WordPair> _saved = Set<WordPair>();   // Add this line.
  final TextStyle _biggerFont = TextStyle(fontSize: 18.0);
  ...
}
```

2. **\_buildRow** 함수에서, 단어 쌍이 아직 저장되지 않았는지 검사하기 위해 **alreadySaved** 변수를 추가합니다.

```dart
Widget _buildRow(WordPair pair) {
  final bool alreadySaved = _saved.contains(pair);  // Add this line.
  ...
}
```

**\_buildRow** 함수에서 즐겨찾기 기능을 추가하기 위해 ListTile 객체에 하트모양의 아이콘을 추가합니다. 다음으로는 하트 아이콘과 상호작용할 수 있는 기능을 추가합니다.

3. 다음과 같이 아이콘을 추가합니다:

```dart
Widget _buildRow(WordPair pair) {
  final bool alreadySaved = _saved.contains(pair);
  return ListTile(
    title: Text(
      pair.asPascalCase,
      style: _biggerFont,
    ),
    trailing: Icon(   // Add the lines from here...
      alreadySaved ? Icons.favorite : Icons.favorite_border,
      color: alreadySaved ? Colors.red : null,
    ),                // ... to here.
  );
}
```

4. 핫 리로드를 하면 각 로우에 열린 하트를 확인할 수 있습니다. 아직 상호작용 기능은 동작하지 않습니다.

![이미지1](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/b01521bf7a6e9f61.png)
![이미지2](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/eca56f73115e3550.png)

### 문제가 있나요?

App이 정확히 동작하지 않을 경우, 다음 링크의 코드와 비교해 볼 수 있습니다.

- [lib/main.dart](https://github.com/flutter/codelabs/blob/master/startup_namer/step5_add_icons/lib/main.dart)

## 상호작용 기능 추가

이번 단계에서는, 하트 아이콘을 탭할 수 있도록 만듭니다. 사용자가 리스트의 요소를 탭하면 즐겨찾기 상태가 토글되면서 즐겨찾기 저장소에 저장되거나 리스트에서 삭제됩니다.

이렇게 구현하기 위해, **\_buildRow** 함수를 수정해야 합니다. 단어 쌍이 이미 즐겨찾기 저장소에 추가되어있다면 삭제합니다. 타일(List Tile)을 탭할 때 마다 상태가 변경되었음을 프레임워크에 알리기 위해 **setState()** 함수가 호출됩니다.

1. **onTap** 함수를 다음과 같이 추가합니다:

```dart
Widget _buildRow(WordPair pair) {
  final alreadySaved = _saved.contains(pair);
  return ListTile(
    title: Text(
      pair.asPascalCase,
      style: _biggerFont,
    ),
    trailing: Icon(
      alreadySaved ? Icons.favorite : Icons.favorite_border,
      color: alreadySaved ? Colors.red : null,
    ),
    onTap: () {      // Add 9 lines from here...
      setState(() {
        if (alreadySaved) {
          _saved.remove(pair);
        } else {
          _saved.add(pair);
        }
      });
    },               // ... to here.
  );
}
```

> **TIP** Flutter의 반응형 스타일 프레임워크에서 **setState()**를 호출하면 State 객체의 **build()** 메서드가 호출되고 그 결과로 UI를 갱신하게 됩니다.

App을 핫 리로드합니다. 즐겨찾기 표시를 하거나 해제하기 위해 아무 타일이나 탭을 해 봅니다. 타일을 탭하면 해당 지점에서 잉크 번짐 애니메이션이 생성됩니다.

![이미지3](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/8caedd467e066f03.png)
![이미지4](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/9d55495e865ccdaf.png)

### 문제가 있나요?

App이 정확히 동작하지 않을 경우, 다음 링크의 코드와 비교해 볼 수 있습니다.

- [lib/main.dart](https://github.com/flutter/codelabs/blob/master/startup_namer/step6_add_interactivity/lib/main.dart)

## 새로운 화면으로 이동하기

이번 단계에서는, 즐겨찾기 표시한 단어 쌍만 출력하는 새로운 페이지(Flutter에서 경로-route-라고 불리우는)를 추가합니다. 이번 단계를 통해 홈 경로와 새로운 경로(route)사이를 이동하는 방법에 대해 배우게 됩니다.

Flutter에서는 Navigator가 App의 경로들이 포함된 스택을 관리합니다. Navigator의 스택에 경로를 추가하면 해당 경로가 출력됩니다. Navigator 스택에서 경로를 빼내면 이전 경로가 출력됩니다.

다음으로, RandomWordsState에 있는 build 메서드에서 AppBar에 리스트 아이콘을 추가합니다. 사용자가 리스트 아이콘을 클릭하면 즐겨찾기 표시한 단어 쌍을 포함한 새로운 경로가 Navigator에 추가되고 아이콘이 출력됩니다.

```dart
class RandomWordsState extends State<RandomWords> {
  ...
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Startup Name Generator'),
        actions: <Widget>[      // Add 3 lines from here...
          IconButton(icon: Icon(Icons.list), onPressed: _pushSaved),
        ],                      // ... to here.
      ),
      body: _buildSuggestions(),
    );
  }
  ...
}
```

> **TIP** 단일 자식 위젯(**child**)을 갖는 위젯도 있고, 여러 자식 위젯(**children**)을 갖는 위젯도 있습니다. 여러 자식 위젯을 정의할 때는 대괄호([])로 배열을 표현합니다.

1. RandomWordsState 클래스에 **\_pushSaved()** 함수를 추가합니다.

```dart
void _pushSaved() {
}
```

2. App을 핫 리로드합니다. App Bar에 리스트 아이콘이 출력됩니다. 이 아이콘을 탭해도 아직은 아무런 동작도 하지 않습니다.

다음에는, Navigator 스택에 경로를 추가합니다. 이 행위에 의해 새로운 화면으로 이동하게 됩니다. 새로운 페이지에 포함된 컨텐츠는 MaterialPageRoute의 익명함수인 **builder** 속성에서 만들어집니다.

3. 아래 코드와 같이 **Navigator.push**를 호출하면, Navigator의 스택에 새로운 경로가 추가됩니다. IDE는 코드에 문제가 있다고 출력하겠지만 곧 작동하는 코드를 추가하게 될 겁니다.

```dart
void _pushSaved() {
  Navigator.of(context).push(
  );
}
```

다음으로, MaterialPageRoute와 builder를 추가해야 합니다. 우선 지금은 ListTile 로우를 생성하는 코드를 작성합니다. ListTile의 **divideTiles()** 메서드는 ListTile간 수평 여백을 추가합니다. **divided** 변수는 **toList()** 함수에 의해 리스트로 변환된 마지막 로우들을 보관합니다.

4. 아래 코드를 추가하세요:

```dart
void _pushSaved() {
  Navigator.of(context).push(
    MaterialPageRoute<void>(   // Add 20 lines from here...
      builder: (BuildContext context) {
        final Iterable<ListTile> tiles = _saved.map(
          (WordPair pair) {
            return ListTile(
              title: Text(
                pair.asPascalCase,
                style: _biggerFont,
              ),
            );
          },
        );
        final List<Widget> divided = ListTile
          .divideTiles(
            context: context,
            tiles: tiles,
          )
          .toList();
      },
    ),                       // ... to here.
  );
}
```

builder 속성은 새로운 경로에 "Saved Suggestions - 저장된 제안들"이란 이름의 App Bar를 포함하는 Scaffold를 반환합니다. 새로운 경로는 ListTiles 로우를 포함하는 ListView로 구성되며 각 로는 구분선으로 분리됩니다.

5. 아래 코드와 같이 수평 구분선을 추가합니다:

```dart
void _pushSaved() {
  Navigator.of(context).push(
    MaterialPageRoute<void>(
      builder: (BuildContext context) {
        final Iterable<ListTile> tiles = _saved.map(
          (WordPair pair) {
            return ListTile(
              title: Text(
                pair.asPascalCase,
                style: _biggerFont,
              ),
            );
          },
        );
        final List<Widget> divided = ListTile
          .divideTiles(
            context: context,
            tiles: tiles,
          )
              .toList();

        return Scaffold(         // Add 6 lines from here...
          appBar: AppBar(
            title: Text('Saved Suggestions'),
          ),
          body: ListView(children: divided),
        );                       // ... to here.
      },
    ),
  );
}
```

6. App을 핫 리로드합니다. 선택한 항목 중 일부를 즐겨찾기하고 App Bar에서 리스트 아이콘을 탭합니다. 즐겨찾기한 항목들이 출력되는 새로운 경로가 출력됩니다. Navigator가 App Bar에 "뒤로가기-Back" 버튼을 추가합니다. 뒤로가기 위해서 명시적으로 Navigator.pop 기능을 구현할 필요가 없습니다. "Back" 버튼을 탭하면 홈 경로로 이동합니다.

![이미지5](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/6e06cd704eec2745.png)
![이미지6](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/a9f9050220d86dad.png)

### 문제가 있나요?

App이 정확히 동작하지 않을 경우, 다음 링크의 코드와 비교해 볼 수 있습니다.

- [lib/main.dart](https://github.com/flutter/codelabs/blob/master/startup_namer/step7_navigate_route/lib/main.dart)

## 테마를 이용해서 UI 변경하기

다음 단계에서는, App의 테마를 변경합니다. 테마는 App의 룩앤필을 제어합니다. 실제 기기나 에뮬레이터에서 제공하는 기본 테마를 사용하거나 브랜딩을 반영하는 사용자정의된 테마를 사용할 수 있습니다.

[ThemeData](https://docs.flutter.io/flutter/material/ThemeData-class.html) 클래스를 설정하는 것으로 App의 테마를 쉽게 변경할 수 있습니다. 지금 만들고 있는 App은 기본 테마를 사용하고 있지만 App의 주요 색상(primary color)을 흰색으로 변경할 수 있습니다.

1. MyApp 클래스에서 색상 변경하기:

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Startup Name Generator',
      theme: ThemeData(          // Add the 3 lines from here...
        primaryColor: Colors.white,
      ),                         // ... to here.
      home: RandomWords(),
    );
  }
}
```

2. App을 핫 리로드합니다. 전체 배경색이 App Bar를 포함해서 이제 모두 흰색으로 변경되었습니다.

또 다른 연습으로 ThemeData를 사용해서 UI의 다른 부분을 변경해 보세요. Material 라이브러리의 [Colors](https://docs.flutter.io/flutter/material/Colors-class.html) 클래스는 사용할 수 있는 많은 색상을 제공하고, 핫 리로드를 사용해서 빠르게 적용해 볼 수 있습니다.

![이미지7](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/a8a58e812150f3c.png)
![이미지8](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/img/2ae336b38fe4c72a.png)

### 문제가 있나요?

App이 정확히 동작하지 않을 경우, 다음 링크의 코드와 비교해 볼 수 있습니다.

- [lib/main.dart](https://github.com/flutter/codelabs/blob/master/startup_namer/step8_themes/lib/main.dart)

## 잘 했어요!

iOS와 Android 모두에서 작동하는 상호작용 Flutter App을 만들어 보았습니다. 이번 코드 실습에서 배운 것은 다음과 같습니다:

- Dart code 작성하기
- 빠른 개발을 위해 핫 리로드 사용하기
- App에 상호작용하는 기능을 추가하기 위해 상태 저장 위젯을 구현하는 방법
- 홈 경로와 새로운 경로 사이를 이동하기 위해 경로를 생성하고 코드를 작성하기
- 테마를 사용해서 UI의 룩앤필을 변경하는 방법

## 다음에 배울 것들

Flutter SDK에 대해 더 배우고 싶다면:

- [Flutter의 레이아웃 만들기](https://flutter.dev/docs/development/ui/layout) 강좌
- [상호작용 기능 추가하기](https://flutter.dev/docs/development/ui/interactive) 강좌
- [위젯 소개 문서](https://flutter.dev/docs/development/ui/widgets-intro)
- [Android 개발자를 위한 Flutter](https://flutter.dev/docs/get-started/flutter-for/android-devs)
- [React Native 개발자를 위한 Flutter](https://flutter.dev/docs/get-started/flutter-for/react-native-devs)
- [웹 개발자를 위한 Flutter](https://flutter.dev/docs/get-started/flutter-for/web-devs)
- [Flutter 유튜브 채널](https://www.youtube.com/flutterdev)

그 외의 리소스들:

- [Flutter를 사용해서 Native Mobile App 만들기](https://www.udacity.com/course/build-native-mobile-apps-with-flutter--ud905)
- [Flutter 쿡북](https://flutter.dev/docs/cookbook/)
- [Java에서 Dart로](https://codelabs.developers.google.com/codelabs/from-java-to-dart/#0)
- [Dart 독학: 언어 더 배워보기](https://flutter.dev/docs/resources/bootstrap-into-dart)
