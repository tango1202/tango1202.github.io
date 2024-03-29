---
layout: single
title: "#22. [레거시 C++ 가이드] 전처리기(매크로 상수, 매크로 함수, 조건부 컴파일, include, pragma)"
categories: "legacy-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [매크로 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98)를 쓰지 말고 [열거형 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)를 사용하라.
> * [매크로](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/)로 타입의 별칭을 만들지 말고 [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EC%9E%AC%EC%A0%95%EC%9D%98%EB%B3%84%EC%B9%AD)를 사용하라.
> * [매크로 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)를 쓰지 말고 [인라인 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/)를 사용하라.

> **모던 C++**
> * (C++11~) [특성(attirbute)](https://tango1202.github.io/cpp/modern-cpp-attribute/)이 추가되어 컴파일러에게 부가 정보를 전달하는 방식을 표준화 했습니다.
> * (C++11~) [alignas() 와 alignof()](https://tango1202.github.io/cpp/modern-cpp-etc/#c11-alignas-alignof)가 추가되어 [메모리 정렬 방식](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)을 표준화 됐습니다.
> * (C++11~) [align()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-memory/#c11-align)은 [메모리 정렬](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)된 포인터를 구합니다.
> * (C++11~) [가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 활용한 [가변 매크로](https://tango1202.github.io/cpp/modern-cpp-etc/#c11-%EA%B0%80%EB%B3%80-%EB%A7%A4%ED%81%AC%EB%A1%9C)가 추가되어 C언어와의 호환성이 높아졌습니다.
> * (C++17~) [__has_include](https://tango1202.github.io/cpp/modern-cpp-etc/#c17-__has_include)가 추가되어 [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include) 하기 전에 파일이 존재하는지 확인할 수 있습니다.
> * (C++20~) [모듈](https://tango1202.github.io/cpp/modern-cpp-module/)이 추가되어 [전처리](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/) 사용 방식을 개선하여 컴파일 속도를 향상시키고, [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include) 순서에 따른 종속성 문제, [선언과 정의 분리](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC) 구성의 불편함, 기호 충돌 문제를 해결했습니다.
> * (C++20~) [source_location](https://tango1202.github.io/cpp-stl/modern-cpp-stl-utility/#c20-source_location)이 추가되어 파일명, 줄번호, 칼럼번호, 함수명등의 정보를 제공합니다.

# 개요

C++ 는 다음의 [전처리](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/), 컴파일, 링크의 3가지 과정을 거쳐서 프로그램을 빌드합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/b118642c-af31-42bb-be77-bb1888a41a16)

[전처리기](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/)는 컴파일되기 전에 소스 코드에서 식별자 부분을 대체 목록으로 치환시켜 주거나, 특정 조건에 맞게 코드 블록을 포함시켜 줍니다.

하지만, [전처리기](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/)는 뜻하지 않게 타입 안전성을 해칠 수 있고, 이름 충돌로 인한 오류 발생 확률도 높기 때문에 사용하지 않는게 좋습니다. 특히, [#define 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98)나 [#define() 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%ED%95%A8%EC%88%98)는 [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4), [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/), [인라인 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/), [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 로 사용하시는게 좋습니다.

|항목|내용|
|--|--|
|[#define 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98)|식별자를 대체 목록으로 치환함|
|[#define() 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%ED%95%A8%EC%88%98)|[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들을 대체 목록에 반영하여 치환함|
|[#undef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#undef)|[#define](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98) 정의를 취소함|
|[defined()](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%BB%B4%ED%8C%8C%EC%9D%BC)|주어진 식별자가 [#define](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98) 되었는지 검사함|
|[#if](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%BB%B4%ED%8C%8C%EC%9D%BC)|`if` 제어문과 유사.<br/>조건이 참이면 해당 코드 블록 포함|
|[#elif](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%BB%B4%ED%8C%8C%EC%9D%BC)|`else if` 제어문과 유사.<br/>조건이 참이면 해당 코드 블록 포함|
|[#ifdef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%BB%B4%ED%8C%8C%EC%9D%BC)|주어진 식별자가 [#define](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98) 되었으면 해당 코드 블록 포함|
|[#ifndef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%BB%B4%ED%8C%8C%EC%9D%BC)|주어진 식별자가 [#define](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98)되지 않았으면 해당 코드 블록 포함|
|[#else](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%BB%B4%ED%8C%8C%EC%9D%BC)|`#if`, `#elif`, `#ifdef`, `#ifndef`가 아닌 경우 해당 코드 블록 포함|
|[#endif](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%BB%B4%ED%8C%8C%EC%9D%BC)|`#if`, `#elif`, `#ifdef`, `#ifndef`, `#else`의 끝|
|[#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include)|파일 포함|
|[#line](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)|[`__LINE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)과 [`__FILE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line) 강제 지정|
|[`__LINE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)|현재 파일의 줄번호,<br/>혹은 [#line](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)으로 지정한 줄번호|
|[`__FILE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)|현재 파일명,<br/>혹은 [#line](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)으로 지정한 파일명|
|[#error](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#error-warning)|메시지를 표시하고 컴파일 종료|
|[#warning](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#error-warning)|메시지를 표시하고 컴파일 진행|
|[#pragma](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#pragma)|비표준 컴파일러 동작|

# 매크로 상수

주어진 식별자를 대체 목록으로 치환합니다. 흔히 정수형 상수, 실수형 상수, [문자열 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)에 이름을 붙이거나 [타입의 별칭](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EC%9E%AC%EC%A0%95%EC%9D%98%EB%B3%84%EC%B9%AD)을 작성하기 위해 사용합니다. 

하지만, 단순 치환이기 때문에 뜻하지 않게 타입 안전성을 해칠 수 있고, 이름 충돌로 인한 오류 발생 확률도 높기 때문에 사용하지 않는게 좋습니다.

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

보다는, [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/), [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4), [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)를 사용하는게 좋습니다.

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

[매크로 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98) 치환은 묻지도 따지지도 않고 수행되기 때문에, 다음과 같이 헤더 파일에 [#define](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98)을 사용하면 동료들이 미칠 수 있습니다. 그러니 꼭 중복되지 않을 만한 식별자로 작성하세요.(*대문자 처럼요. [매크로 대문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-naming/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EB%8C%80%EB%AC%B8%EC%9E%90) 참고*)

```cpp
#define public private 
#define class enum
#define GetData 
#define std hello 
#define GetObject // 이건 정말 WindowAPI 에 있음
#define GetForm // 이것도 정말 WindowAPI 에 있음
```

**define 개행**

[#define](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98)은 한줄로 작성하여야 하나, 너무 긴 경우 `\`로 개행하여 작성할 수 있습니다.(*이때 `\`뒤에 어떤 공백도 없어야 합니다.*)

```cpp
// 개행으로 f 함수 매크로문 정의
#define LONGFUNC int f(int x) { \
    return x + x;               \ 
}

// f 함수를 정의함
LONGFUNC;  

int val = f(3);
EXPECT_TRUE(val == 6);
```

# 매크로 함수

 [매크로 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)는 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)들을 대체 목록에 반영하여 치환합니다. 함수라고는 하지만 단순 치환입니다.

 ```cpp
#define SQUARE(x) x * x // (△) 비권장. 인자와 코딩 계약이 안됩니다.

EXPECT_TRUE(SQUARE(2) == 4); // 2 * 2
```

문제 없어 보일 수도 있겠으나, 수식을 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 입력하면 단순 치환에 따라 연산 우선 순위가 꼬여 엉뚱한 결과가 나올 수 있습니다.

```cpp
EXPECT_TRUE(SQUARE(1 + 1) == 3); // (X) 오동작. 연산자 우선 순위가 꼬였습니다. 1 + 1 * 1 + 1
```

이를 해결하는 방법으로 대체되는 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 괄호를 사용하는 방법이 있기는 합니다.

```cpp
#define SQUARE(x) (x) * (x) // (△) 비권장. 연산 우선 순위는 해결됐지만, 여전히 인자와 코딩 계약이 안됩니다.
EXPECT_TRUE(SQUARE(1 + 1) == 4); // (1 + 1) * (1 + 1)
```

연산 우선 순위를 해결했다고 하더라도, 여전히 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입에 따른 **코딩 계약**은 할 수 없습니다. 그러니, **코딩 계약**과 디버깅 편의를 위해 [인라인 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/)를 사용하는게 좋습니다.

 ```cpp
 inline int Square(int x) { return x * x;} // (O) 인자 타입에 따른 코딩 계약, 디버깅 용이
 ```

**매크로 함수 특수 기능**

[매크로 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)에서는 하기의 특수 기능이 있습니다.

|항목|내용|
|--|--|
|`#`|[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 [문자열 상수 리터럴](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)로 변경|
|`##`|[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 연결|

```cpp
#define MAKE_STRING_LITERAL(x) #x
EXPECT_TRUE(MAKE_STRING_LITERAL(Hello World) == "Hello World"); // 문자열로 변경됨

#define MAKE_FUNCTION(prefix, functionName, a) int prefix##functionName() {return a;}
MAKE_FUNCTION(g_, Func, 10); // g_Func 이름의 함수를 정의함
EXPECT_TRUE(g_Func() == 10); // g_Func 호출
```

> *(C++11~) [가변 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 활용한 [가변 매크로](https://tango1202.github.io/cpp/modern-cpp-etc/#c11-%EA%B0%80%EB%B3%80-%EB%A7%A4%ED%81%AC%EB%A1%9C)가 추가되어 C언어와의 호환성이 높아졌습니다.*

# #undef

기존에 정의된 [#define](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98)을 제거합니다.

```cpp
#define PI 3.14
EXPECT_TRUE(PI == 3.14); // (O)

#undef PI // 제거됩니다.
EXPECT_TRUE(PI == 3.14); // (X) 컴파일 오류
```

# 조건부 컴파일

조건부 컴파일을 이용하면, [#define](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#define-%EC%83%81%EC%88%98)으로 정의한 식별자의 존재 유무나 조건에 따라 코드 블록을 포함시킬 수 있습니다. 멀티플랫폼 환경을 지원할 때는 유용할 수도 있지만, 코드 분석을 어렵게 하므로 최소화해야 합니다.

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

# __cplusplus

C++ 인지 확인 하거나, C++ 버전을 확인합니다. C++ 버전별로 호환되는 코드를 작성할때 사용합니다.

버전값은 표준화가 된 년월(`YYYYMM`)로서 다음과 같습니다.

|항목|내용|
|--|--|
|C++98|199711L|
|C++98 TR1|199711L|
|C++11|201103L|
|C++14|201402L|
|C++17|201703L|
|C++20|202002L|

```cpp
#ifndef __cplusplus
    #error C++ is required    
#elif 202002L <= __cplusplus
    // C++20 이상인 코드
#elif 201703L <= __cplusplus
    // C++17 이상인 코드
#elif 201402L <= __cplusplus
    // C++14 이상인 코드    
#elif 201103L <= __cplusplus
    // C++11 이상인 코드        
#else
    // C++98
#endif
```

# #include

헤더 파일을 포함합니다. 마치 [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include)위치에 대상 파일이 코딩된 것처럼 만들어 줍니다.

|항목|내용|
|--|--|
|[#include <>](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include)|컴파일러에 지정된 포함 경로에서 찾음.<br/>주로 C++언어 헤더 파일 포함시 사용|
|[#include ""](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include)|컴파일 중인 경로에서 찾고, 없으면 `<>` 경로에서 찾음.<br/>주로 사용자 헤더 파일 포함시 사용|

> *(C++17~) [__has_include](https://tango1202.github.io/cpp/modern-cpp-etc/#c17-__has_include)가 추가되어 [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include) 하기 전에 파일이 존재하는지 확인할 수 있습니다.*<br/>
> *(C++20~) [모듈](https://tango1202.github.io/cpp/modern-cpp-module/)이 추가되어 [전처리](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/) 사용 방식을 개선하여 컴파일 속도를 향상시키고, [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include) 순서에 따른 종속성 문제, [선언과 정의 분리](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-include/#%EC%84%A0%EC%96%B8%EA%B3%BC-%EC%A0%95%EC%9D%98-%EB%B6%84%EB%A6%AC) 구성의 불편함, 기호 충돌 문제를 해결했습니다.*

# `__LINE__`, `__FILE__`, #line

[`__LINE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)과 [`__FILE__`](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)은 줄번호와 파일명을 나타내는 미리 지정된 [매크로](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/) 입니다. 디버깅시 현재 라인수와 파일명을 표시할 수 있습니다.

```cpp
// Line Number:118 Filename:C:\XXX\XXX.cpp
std::cout << "Line Number:" << __LINE__ << " Filename:" << __FILE__ << std::endl; 
```

[#line](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#line-file-line)은 해당 줄번호와 파일명을 강제로 변경해 줍니다.

```cpp
#line 1234 "test.cpp" 
std::cout << "Line Number:" << __LINE__ << " Filename:" << __FILE__ << std::endl;  // Line Number:1234 Filename:test.cpp      
```

> *(C++20~) [source_location](https://tango1202.github.io/cpp-stl/modern-cpp-stl-utility/#c20-source_location)이 추가되어 파일명, 줄번호, 칼럼번호, 함수명등의 정보를 제공합니다.*

# #error, #warning

[#error](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#error-warning)와 [#warning](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#error-warning)은 조건부 컴파일시 OS 환경이나 컴파일러 환경이나 라이브러리 환경을 검사하고, 컴파일을 중단(*[#error](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#error-warning)*)시키거나 계속 진행(*[#warning](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#error-warning)*)시킵니다.

```cpp
#define OS_WIN
#ifndef OS_WIN
    #error "Only Windows."
#endif
```

# #pragma

비표준 컴파일러 확장 기능입니다. 컴파일러마다 지원 여부는 다를 수 있으니 컴파일러 설명서를 참고해야 합니다.

> *(C++11~) [attribute](https://tango1202.github.io/cpp/modern-cpp-attribute/)가 추가되어 컴파일러에게 부가 정보를 전달하는 방식을 표준화 했습니다.*

**#pragma once**

헤더 파일을 1회만 포함시킵니다.(*[인클루드 가드](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-include/#%EC%9D%B8%ED%81%B4%EB%A3%A8%EB%93%9C-%EA%B0%80%EB%93%9C) 참고*)

```cpp
// 헤더 파일에서
#pragma once // 1회만 포함시킴
...
```

**#pragma pack**

컴파일러는 클래스나 구조체의 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 할당하는데 있어, 메모리 접근 편의를 위해 **[메모리 정렬(Memory Alignment)](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)** 을 합니다.(*[개체 크기와 메모리 정렬](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC) 참고*) 

다음 코드는 [메모리 정렬](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)을 수행하여, `char`(1byte) + 패딩(3byte) + `int`(4byte) = 8byte가 됩니다.

```cpp
class T {
    char m_Char; // 1byte. 3byte 패딩
    int m_Int; // 4byte
};

EXPECT_TRUE(sizeof(T) == 8); 
```

[#pragma pack](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#pragma)을 이용하면, [메모리 정렬](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC) byte 크기를 조정할 수 있어 메모리 낭비를 줄일 수 있습니다.(*다만 메모리 접근 속도는 저하됩니다.*)

```cpp
#pragma pack(push, 1) // 메모리 정렬을 1byte 단위로 설정      
    class T {
        char m_Char; // 1byte 
        int m_Int; // 4byte
    };

    EXPECT_TRUE(sizeof(T) == 5); // 1 + 4 = 5byte
#pragma pack(pop) // 메모리 정렬 설정 원복 
```

> *(C++11~) [alignas() 와 alignof()](https://tango1202.github.io/cpp/modern-cpp-etc/#c11-alignas-alignof)가 추가되어 [메모리 정렬 방식](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)을 표준화 됐습니다.*

**#pragma warning**

컴파일러의 경고 처리 방법을 설정합니다.

|항목|내용|
|--|--|
|`default`|기본값으로 재설정|
|`disable`|지정한 경고 무시|
|`error`|지정한 경고를 오류로 처리|
|`once`|지정한 경고의 메시지를 한번만 표시|

```cpp
#pragma warning(push)
#pragma warning(disable : 4507; once : 4385; error : 164)
#pragma warning(disable : 4508)
#pragma warning(disable : 4509)

코드들...

#pragma warning(pop) // #pragma warning(push) 때의 상태로 전환
```

**#pragma comment(lib, "libname")**

링커가 검색해야할 라이브러리 이름을 지정합니다.

```cpp
#pragma comment(lib, "kernel32")
#pragma comment(lib, "user32")
```

