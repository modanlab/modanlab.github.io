---
title: "Flutter 코드랩: Write your first Flutter app, part 1"
categories:
- Flutter
---

# Write your first Flutter app, part 1

- [Write your first Flutter app, part 1](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1#0)

## StatelessWidget

- [docs: StatelessWidget](https://api.flutter.dev/flutter/widgets/StatelessWidget-class.html)

- Stateless widgets 은 immutable 함
  - properties가 바뀔 수 없다는 뜻
  - 모든 value가 final

- Stateless widgets은 위젯의 lifetime 동안 변경될 수 있는 state를 유지함

- Stateless widgets을 implementing하려면 최소 2개의 StatelessWidget 클래스가 필요함
  - StatelessWidget은 State 클래스 인스턴스를 생성함

- StatefulWidget 객체는 자체로 immutable 하고 폐기, 재생성 될 수 있지만 State 객체는 위젯의 lifetime 동안 지속됨

- stful 이라고 치면 StatefulWidget이 나옴


```dart
class RandomWords extends StatefulWidget {
  const RandomWords({Key? key}) : super(key: key);

  @override
  State<RandomWords> createState() => _RandomWordsState();
}

class _RandomWordsState extends State<RandomWords> {
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}

```

## Scaffold

- [docs: Scaffold](https://api.flutter.dev/flutter/material/Scaffold-class.html)

## build

- [docs: build](https://api.flutter.dev/flutter/widgets/SliverPersistentHeaderDelegate/build.html)

## ListView 구현

{% include figure.html image="https://github.com/modanlab/modanlab.github.io/blob/main/assets/images/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-10-19%20%EC%98%A4%EC%A0%84%203.58.31.png?raw=true" alt="Flutter ListView" %}
