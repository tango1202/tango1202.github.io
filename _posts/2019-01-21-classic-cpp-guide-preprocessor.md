---
layout: single
title: "#21. [고전 C++ 가이드] 전처리기(매크로 상수, 매크로 함수, 조건부 컴파일, include, pragma)(작성중)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 매크로 상수를 쓰지 말고 열거형 상수를 사용하라.
> * 매크로로 타입의 별칭을 만들지 말고 `typedef`를 사용하라.
> * 매크로 함수를 쓰지 말고 인라인 함수를 사용하라.

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
|`#else`|`#if`, `#elif`, `#ifdef`, `#ifndef`가 아닌 경우 해당 코드 블록 포함|
|`#endif`|`#if`, `#elif`, `#ifdef`, `#ifndef`, `#else`의 끝|
|`#include`|파일 포함|
|`#line`|`__LINE__`과 `__FILE__` 강제 지정|
|`__LINE__`|현재 파일의 줄번호,<br/>혹은 `#line`으로 지정한 줄번호|
|`__FILE__`|현재 파일명,<br/>혹은 `#line`으로 지정한 파일명|
|`#error`|메시지를 표시하고 컴파일 종료|
|`#warning`|메시지를 표시하고 컴파일 진행|
|`#pragma`|비표준 컴파일러 동작|

전처리기는 컴파일되기 전에 소스 코드에서 식별자 부분을 대체 목록으로 치환하거나, 특정 조건에 맞게 코드 블록을 포함하거나 제거하는 역할을 합니다. 멀티플랫폼 환경을 지원할 때는 유용할 수도 있지만, 코드 분석을 어렵게 하므로 최소화해야 합니다.

# `#define` 상수

주어진 식별자를 대체 목록으로 치환합니다. 흔히 정수형 상수, 실수형 상수, 문자열 상수에 이름을 붙이거나 타입의 별칭을 작성하기 위해 사용합니다. 

하지만, 단순 치환이기 때문에 뜻하지 않게 타입 안정성을 해칠 수 있고, 이름 충돌로 인한 오류 발생도 높기 때문에 사용하지 않는게 좋습니다.

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

`#define` 치환은 묻지도 따지지도 않고 수행되기 때문에, 헤더 파일에 다음과 같이 `#define`을 사용하면 동료들이 미칠 수 있습니다. 그러니 절대로 중복되지 않을 만한 식별자로 작성하세요.(대문자 처럼요. [매크로 대문자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-naming/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EB%8C%80%EB%AC%B8%EC%9E%90) 참고)

```cpp
#define public private 
#define class enum
#define GetData 
#define std hello 
#define GetObject // 이건 정말 WindowAPI 에 있음
#define GetForm // 이것도 정말 WindowAPI 에 있음
```

`#define`은 한줄로 작성하여야 하나, 너무 긴 경우 `\`로 개행하여 작성할 수 있습니다.(이때 `\`뒤에 어떤 공백도 없어야 합니다.)

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

# `#define` 함수

 매크로 함수는 인자들을 대체 목록에 반영하여 치환합니다. 함수라고는 하지만 단순 치환입니다.

 ```cpp
#define SQUARE(x) x * x // (△) 비권장. 인자와 코딩 계약이 안됩니다.

EXPECT_TRUE(SQUARE(2) == 4); // 2 * 2
```

문제 없어 보일 수도 있겠으나, 수식을 인자로 입력하면 단순 치환에 따라 연산 우선 순위가 꼬여 엉뚱한 결과가 나올 수 있습니다.

```cpp
EXPECT_TRUE(SQUARE(1 + 1) == 3); // (X) 오동작. 연산자 우선 순위가 꼬였습니다. 1 + 1 * 1 + 1
```

이를 해결하는 방법으로 대체되는 인자에 괄호를 사용하는 방법이 있기는 합니다.

```cpp
#define SQUARE(x) (x) * (x) // (△) 비권장. 인자와 코딩 계약이 안됩니다.
EXPECT_TRUE(SQUARE(1 + 1) == 4); // (1 + 1) * (1 + 1)
```

하지만, 매크로 함수 보다는, 인자 타입에 따른 **코딩 계약**과 디버깅 편의를 위해 [인라인 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-inline/)를 사용하는게 좋습니다.

 ```cpp
 inline int Square(int x) { return x * x;} // (O) 인자 타입에 따른 코딩 계약, 디버깅 용이
 ```
 
매크로 함수에서는 하기의 특수 기능이 있습니다.

|항목|내용|
|--|--|
|`#`|인자를 문자열 리터럴로 변경|
|`##`|인자를 연결|

```cpp
#define MAKE_STRING_LITERAL(x) #x
EXPECT_TRUE(MAKE_STRING_LITERAL(Hello World) == "Hello World"); // 문자열로 변경됨

#define MAKE_FUNCTION(prefix, functionName, a) int prefix##functionName() {return a;}
MAKE_FUNCTION(g_, Func, 10); // g_Func 이름의 함수를 정의함
EXPECT_TRUE(g_Func() == 10); // g_Func 호출
```

# `#undef`

기존에 정의된 `#define`을 제거합니다.

```cpp
#define PI 3.14
EXPECT_TRUE(PI == 3.14); // (O)

#undef PI // 제거됩니다.
EXPECT_TRUE(PI == 3.14); // (X) 컴파일 오류
```

# 조건부 컴파일

조건부 컴파일 기능을 이용하면, `#define`으로 정의한 식별자의 존재 유무에 따라, 특정 조건에 맞게 코드 블록을 포함하거나 제거하는 역할을 합니다. 멀티플랫폼 환경을 지원할 때는 유용할 수도 있지만, 코드 분석을 어렵게 하므로 최소화해야 합니다.

```cpp
#define MY_DEBUG // MY_DEBUG 정의 유무만 알면 되므로 꼭 대체 목록을 작성할 필요 없음

int status = 0;
#if defined(MY_DEBUG)
    status = 1;
#else 
    status = 2;
#endif

EXPECT_TRUE(status == 1); // MY_DEBUG가 정의되어 1
```

# `#include`

헤더 파일을 포함합니다. 마치 `#include`위치에 대상 파일이 코딩된 것처럼 만들어 줍니다.

|항목|내용|
|--|--|
|`#include <>`|컴파일러에 지정된 포함 경로에서 찾음.<br/>주로 C++언어 헤더 파일 포함시 사용|
|`#include ""`|컴파일 중인 경로에서 찾고, 없으면 `<>` 경로에서 찾음.<br/>주로 사용자 헤더 파일 포함시 사용|

# `__LINE__`, `__FILE__`, `#line`

`__LINE__`과 `__FILE__` 은 줄번호와 파일명을 나타내는 미리 지정된 매크로 입니다. 디버깅시 현재 라인수와 파일명을 표시할 수 있습니다.

```cpp
// Line Number:118 Filename:C:\XXX\XXX.cpp
std::cout<<"Line Number:"<<__LINE__<<" Filename:"<<__FILE__<<std::endl; 
```

`#line`은 해당 줄번호와 파일명을 강제로 변경해 줍니다.

```cpp
#line 1234 "test.cpp" 
    std::cout<<"Line Number:"<<__LINE__<<" Filename:"<<__FILE__<<std::endl;  // Line Number:1234 Filename:test.cpp      
```
# `#error`, `#warning`

`#error`와 `#warning`은 조건부 컴파일시 OS 환경이나 컴파일러 환경이나 라이브러리 환경을 검사하고, 컴파일을 중단(`#error`)시키거나 계속 진행(`#warning`)시킵니다.

```cpp
#define OS_WIN
#ifndef OS_WIN
    #error "Only Windows."
#endif
```

# `#pragma`

비표준 컴파일러 확장 기능입니다. 컴파일러마다 지원 여부는 다를 수 있으니 컴파일러 설명서를 참고해야 합니다.

**`#pragma once`**

헤더 파일을 1회만 포함시킵니다.([인클루드 가드](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-include-guard/) 참고)

```cpp
// 헤더 파일에서
#pragma once // 1회만 포함시킴
...
```

**`#pragma pack`**

클래스나 구조체의 멤버 변수를 할당하는데 있어, 메모리 접근 편의를 위해 4byte단위로 멤버 변수를 할당합니다.(이를 패딩(padding)이라 합니다.) 

```cpp
class T {
    char m_Char; // 1byte, 메모리 접근 편의를 위해 32bit(4byte) 단위로 할당(패딩). 3byte 빈공간이 생김 
    int m_Int; // 4byte
};

EXPECT_TRUE(sizeof(T) == 8); // char가 패딩됨
```

상기 코드를 보면 `T`는 `char`와 `int`로 구성되어 5byte가 되어야 할 것 같지만, 4byte 단위로 멤버 변수가 할당되어 8byte가 되는 것을 알 수 있습니다.

`#pragma pack`을 이용하면, 데이터 버스 크기를 주어진 바이트 크기로 조정할 수 있어 메모리 낭비를 줄일 수 있습니다.(다만 메모리 접근 속도는 저하됩니다.)

```cpp
#pragma pack(push, 1) // 데이터 버스 크기를 1 바이트 단위로 설정      
    class T {
        char m_Char; // 1byte 
        int m_Int; // 4byte
    };

    EXPECT_TRUE(sizeof(T) == 5); // 1 + 4 = 5byte
#pragma pack(pop) // 데이터 버스 크기 설정 원복 
```

**pragma comment**

**pragma warning**

