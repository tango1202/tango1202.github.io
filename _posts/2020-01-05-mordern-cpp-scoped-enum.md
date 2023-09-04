---
layout: single
title: "#5. [모던 C++] (C++11~) 범위 있는 열거형"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

기존 열거형은 열거형내 이름이 한정되지 않아 이름 충돌의 우려가 있어 클래스 내에 정의([열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/) 참고)하는 걸 선호 했습니다만, 이제 범위 있는 열거형을 지원합니다.

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