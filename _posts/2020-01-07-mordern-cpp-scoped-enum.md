---
layout: single
title: "#7. [모던 C++] (C++11~) 범위 있는 열거형"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 이름 범위를 한정하는 범위 있는 열거형이 추가되어 이름 충돌 회피가 쉬워졌습니다.

# 개요

기존 열거형은 열거형내 이름이 한정되지 않아 이름 충돌의 우려가 있어 클래스 내에 정의([열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/) 참고)하는 방식을 사용했습니만, 이제 범위 있는 열거형을 지원하여 이름 충돌 회피가 훨씬 쉬워졌습니다.

```cpp
// 기존
enum Week {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week week = Sunday; // (△) 비권장. 이름 충돌이 쉬움

// 범위 있는 열거형
enum class Week {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week week = Week::Sunday; // 범위명을 지정하여 이름 충돌 회피
```