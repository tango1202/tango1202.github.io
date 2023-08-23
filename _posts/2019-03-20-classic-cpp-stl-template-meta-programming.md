---
layout: single
title: "#20. [고전 C++ STL] 템플릿 메타 프로그래밍"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

템플릿들은 인스턴스화 되면서 코드를 생성하다 보니 컴파일 타임에 여러가지 프로그래밍이 가능하다는게 발견되었고, 이를 템플릿 메타 프로그래밍이라 합니다.

|항목|내용|
|--|--|
|조건 분기|템플릿과 템플릿 일반화로 구현|
|반복|재귀 호출로 구현|
|변수|열거형 상수로 구현|

다음은 컴파일 타임에 팩토리얼(1~N 까지의 곱) 을 구하는 예입니다.

다음 코드를 보면,

1. `Factorial<5>::val`에서 열거형 상수값을 가져오면, 재귀적으로 `Factorial<4>::val`의 값을 가져와 곱합니다.
2. `Factorial<0>::val`은 `1`을 곱하여, 결과값에 영향을 주지 않고, 재귀적 호출을 중단합니다.

```cpp
// Factorial n -1 을 재귀 호출합니다.
template<unsigned int n>
struct Factorial { 
    enum {
        val = n * Factorial<n-1>::val
    };  
};
// 0일때 특수화 버전. 더이상 재귀 호출을 안합니다.
template<>
struct Factorial<0> { 
    enum {
        val = 1
    }; 
};
// 컴파일타임에 계산된 120이 val에 대입됩니다.
unsigned int val = Factorial<5>::val; 
EXPECT_TRUE(val == 1 * 2 * 3 * 4 * 5);
```
