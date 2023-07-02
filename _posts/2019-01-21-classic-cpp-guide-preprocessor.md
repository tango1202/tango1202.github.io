---
layout: single
title: "#21. [고전 C++ 가이드] 전처리기(매크로 상수, 매크로 함수, 조건부 포함, include, Pragma)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 매크로 상수를 쓰지 말고 열거형 상수를 사용하라.
> * 매크로로 타입의 별칭을 만들지 말고 `typedef`를 사용하라.
> * 매크로 함수를 쓰지 말고 인라인 함수를 사용하라.
> * typedef

# 개요

|항목|내용|
|--|--|
|`#define` 상수|식별자를 대체 목록으로 치환함|
|`#define()` 함수|인자들을 대체 목록에 반영하여 치환함|
|`#undef`|`define` 정의를 취소함|
|`defined()`|주어진 식별자가 `define` 되었는지 검사함|
|`#if`|`if` 제어문과 유사.<br/>조건이 참이면 해당 코드 블럭 포함|
|`#elif`|`else if` 제어문과 유사.<br/>조건이 참이면 해당 코드 블럭 포함|
|`#ifdef`|주어진 식별자가 `define` 되었으면 해당 코드 블록 포함|
|`#ifndef`|주어진 식별자가 `define`되지 않았으면 해당 코드 블록 포함|
|`#else`|`#if`, `#elif`, `#ifdef`, `#ifndef`가 아닌 경우 해당 코드블록 포함|
|`#endif`|`#if`, `#elif`, `#ifdef`, `#ifndef`, `#else`의 끝|
|`#include`|파일 포함|
|`#line`|`__LINE__`과 `__FILE__` 강제 지정|
|`__LINE__`|현재 파일의 라인번호,<br/>혹은 `#line`으로 지정한 줄번호
|`__FILE__`|현재 파일의 이름, <br/>혹은 `#line`으로 지정한 파일명|
|`#error`|메시지를 표시하고 컴파일 종료|
|`#warning`|메시지를 표시하고 컴파일 진행|
|`#pragma`|비표준 컴파일러 동작|

전처리기는 컴파일되기 전에 소스 코드에서 식별자 부분을 대체 목록으로 치환하거나, 특정 조건에 맞게 코드 블록을 포함하거나 제거하는 역할을 합니다. 멀티플랫폼 환경을 지원할 때는 유용할 수도 있지만, 코드 분석을 어렵게 하므로 최소화해야 합니다.

# `define` 상수

주어진 식별자를 대체 목록으로 치환합니다. 흔히 정수형 상수, 실수형 상수, 문자열 상수에 이름을 붙이거나 자료형의 용도에 따라 별칭을 작성하기 위해 사용합니다. 

하지만, 단순 치환이기 때문에 의도치 않은 타입 안정성을 해칠 수 있고, 이름 충돌로 인한 오류 발생도 높기 때문에 사용하지 않는게 좋습니다.

```cpp
#define SUNDAY 0 // (△) 비권장. enum Val {Sunday, Monday, Tuesday}; 가 낫다
#define MONDAY 1
#define TUESDAY 2

#define PI 3.14 // (△) 비권장. const double g_Pi = 3.14;가 낫다.
#define WELCOM "Hello World" // (△) 비권장. const char* g_Welcome = "Hello World";가 낫다.
#define LOGICAL int // (△) 비권장. typedef int Logical; 이 낫다.

EXPECT_TRUE(SUNDAY == 0);
EXPECT_TRUE(MONDAY == 1);   
EXPECT_TRUE(TUESDAY == 2);    

EXPECT_TRUE(PI == 3.14);   
EXPECT_TRUE(WELCOM == "Hello World"); 
EXPECT_TRUE(typeid(LOGICAL) == typeid(int)); 
```

보다는

```cpp
class Week {
public:
        enum Val { // (O) 매크로로 치환된 정수가 아닌 Week::Val 타입으로 코딩계약을 할 수 있습니다.
        Sunday, 
        Monday, 
        Tuesday,
    };
};
const double g_Pi = 3.14; // (O) 타입에 기반한 코딩 계약이 가능합니다.
const char* g_Welcome = "Hello World"; // (O) 타입에 기반한 코딩 계약이 가능합니다.
typedef int Logical; // (O) 타입에 기반한 코딩 계약이 가능합니다.

Week::Val week = Week::Monday;
EXPECT_TRUE(week == Week::Monday);
EXPECT_TRUE(g_Pi == 3.14);   
EXPECT_TRUE(g_Welcome == "Hello World"); 
EXPECT_TRUE(typeid(Logical) == typeid(int)); 
```

이 낫습니다.

이러한 치환은 묻지도 따지지도 않고 수행되기 때문에, 헤더파일에 하기처럼 `define`을 사용하게 되면 동료들이 미칠 수 있습니다.

```cpp
#define public private 
#define class enum
#define GetData 
#define std hello 
#define GetObject // 이건 정말 WindowAPI 에 있음
#define GetForm // 이것도 정말 WindowAPI 에 있음
```

`define`은 한줄로 작성하여야 하나, 너무 긴 경우 `\`로 개행하여 작성할 수 있습니다.(이때 `\`뒤에 어떤 공백도 없어야 합니다.)

```cpp
// 개행을 사용하여 f 함수 매크로문 정의
#define LONGFUNC int f(int x) { \
    return x + x;               \ 
}

// f 함수를 정의함
LONGFUNC;  

int val = f(3);
EXPECT_TRUE(val == 6);
```

# `define` 함수

 매크로 함수는 인자들을 대체 목록에 반영하여 치환합니다. 함수라고는 하지만 단순 치환입니다.

 ```cpp
#define SQUARE(x) x * x // (△) 비권장. 인자와 코딩 계약이 안됩니다.

EXPECT_TRUE(SQUARE(2) == 4); // 2 * 2
```
문제 없어 보일 수도 있겠으나, 단순 치환하다 보니 하기처럼 수식을 입력하면 연산 우선 순위가 꼬여 엉뚱한 결과가 나올 수 있습니다.

```cpp
EXPECT_TRUE(SQUARE(1 + 1) == 3); // 1 + 1 * 1 + 1
```

이를 해결하는 방법으로 하기와 같이 괄호를 사용하는 방법이 있기는 합니다.

```cpp
#define SQUARE(x) (x) * (x) // (△) 비권장. 인자와 코딩 계약이 안됩니다.
EXPECT_TRUE(SQUARE(1 + 1) == 4); // (1 + 1) * (1 + 1)
```

하지만, 매크로 함수 보다는, 인자 타입에 따른 코딩 계약과 디버깅 편의를 위해 [인라인 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-inline/)를 사용하는게 좋습니다.

 ```cpp
 inline int Square(int x) { return x * x;} // (O) 인자 타입에 따른 코딩 계약, 디버깅 용이
 ```
 
매크로 함수에서는 하기의 특수 기능이 있습니다.

|항목|내용|
|--|--|
|`#`|인자를 문자열 리터럴로 변경|
|`##`|인자를 연결해줌|

```cpp

    #define MAKE_STRING_LITERAL(x) #x
    EXPECT_TRUE(MAKE_STRING_LITERAL(Hello World) == "Hello World"); // 매크로 함수 결과 문자열로 변경됨

    #define MAKE_FUNCTION(prefix, functionName, a) int prefix##functionName() {return a;}
    MAKE_FUNCTION(g_, Func, 10); // g_Func 이름의 함수를 정의함
    EXPECT_TRUE(g_Func() == 10); // 매크로 함수 결과 g_Func 이라는 이름으로 합성됨
```




```cpp
 #ifdef MACRO_NAME
expr;
#else
expr;
#endif
```
전처리 컴파일 매크로. 매크로 정의 상태에 따라 선택적 컴파일. C++ 문법적으로 극복할 수는 있으나, 아직은 참을 수 없는 유혹







undef

#if
#ifdef
#ifndef
#3elif
#else
#endif

#include <> 시스템 헤더파일. 컴파일러에 지정된 include 경로

#include "" 사용자 헤더파일. 컴파일 중인 경로. 없으면 <> 경로

#pragma : 비표준

#pragma once

#pragma pack(arg) 메모리 정렬 제어

#include 가드

