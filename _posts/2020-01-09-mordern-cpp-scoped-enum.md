---
layout: single
title: "#9. [모던 C++] (C++11~) 범위 있는 열거형"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 이름 범위를 한정하는 범위 있는 열거형이 추가되어 이름 충돌 회피가 쉬워졌습니다.

# 개요

기존에는 열거형의 이름이 한정되지 않아 이름 충돌의 우려가 있어 클래스 내에 정의하는 방식을 사용했었는데요,([열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/) 참고) 

```cpp
// 기존
enum Week {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week week{Sunday}; // (△) 비권장. 이름 충돌이 쉬움

// 혹은
class Week {
public:
    // 클래스내에 정의. 사용시 클래스명을 기재해야 함
    enum Val {
        Sunday, Monday, Tuesday, Wednesday, 
        Thursday, Friday, Saturday
    };
};

Week::Val val{Week::Sunday}; // 범위 확인 연산자와 클래스명 사용
```

C++11 부터는 범위 있는 열거형을 지원하여 이름 충돌 회피가 훨씬 쉬워졌습니다.

```cpp
// 범위 있는 열거형
enum class Week {
    Sunday, Monday, Tuesday, Wednesday, 
    Thursday, Friday, Saturday
};
Week week{Week::Sunday}; // 범위명을 지정하여 이름 충돌 회피
```