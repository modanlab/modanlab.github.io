---
title: "Flutter 코드랩: 지루한 Flutter 앱을 멋지게 바꿔보세요"
categories:
- Flutter
---

# 지루한 Flutter 앱을 멋지게 바꿔보세요

- [지루한 Flutter 앱을 멋지게 바꿔보세요](https://codelabs.developers.google.com/codelabs/flutter-boring-to-beautiful?hl=ko#0)


## import

```dart
import 'package:adaptive_components/adaptive_components.dart';
import 'package:flutter/material.dart';

import '../../../shared/classes/classes.dart';
import '../../../shared/extensions.dart';
import '../../../shared/providers/providers.dart';
import '../../../shared/views/views.dart';
import '../../playlists/view/playlist_songs.dart';
import 'view.dart';
```

- import 사용

## budil

```dart
class _HomeScreenState extends State<HomeScreen> {
  @override
  Widget build(BuildContext context) {
```

- build는 위젯 트리의 루트를 만듦

## 아이콘 변경

{% include figure.html image="https://github.com/modanlab/modanlab.github.io/blob/main/assets/images/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-10-21%20%EC%98%A4%EC%A0%84%203.02.57.png?raw=true" alt="아이콘 변경" %}


## 명령어

- flutter pub add google_fonts
  - google_fonts 패키지 가져오는 명령어

## 폰트 변경

{% include figure.html image="https://github.com/modanlab/modanlab.github.io/blob/main/assets/images/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-10-21%20%EC%98%A4%EC%A0%84%203.20.56.png?raw=true" alt="폰트 변경" %}

