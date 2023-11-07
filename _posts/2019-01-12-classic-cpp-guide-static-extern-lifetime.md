---
layout: single
title: "#12. [고전 C++ 가이드] 정적 변수와 정적 함수(static), 전역 선언(extern), 개체 수명"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 변수의 수명(*[유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)*)은 짧을 수록 좋다. 최대한 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)나 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)로 사용하라.
> * [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)나 [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98) 보다는 [함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)를 사용하라.
> * 컴파일러 최적화가 쉽도록 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 사용하라.

> **모던 C++**
> * (C++11~) [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 위해 [우측값 참조(`&&`)](??)가 추가되어 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다.
> * (C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.
> * (C++17~) [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, [클래스 정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 정의 및 초기화가 쉬워졌습니다.

# 개요

일반적으로 메모리 공간을 오랫동안 차지하는 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)나 정적 변수보다는 사용이 끝나면 메모리를 바로 반납하는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)나 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)가 메모리 관리에 좋습니다. 시스템 자원을 관리하는 등의 ***수명이 길어야 할 이유가 없다면, [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)나 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)로 작성***하시기 바랍니다.

수명 주기에 따라 다음과 같이 변수를 구분할 수 있습니다.

|항목|내용|수명|
|--|--|--|
|[지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)|블록 안에서만 사용 가능한 변수| `{` ~ `}` 범위에서 생성과 소멸|
|[전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)|모든 파일에서 사용 가능한 변수<br/>제어가 처음으로 정의를 통과할때 생성됨|`main` 호출 ~ `main` 종료|
|[정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)|현재 파일에서만 사용할 수 있는 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)|`main` 호출 ~ `main` 종료|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|특정 개체에 속하는 변수|개체 생성시 생성자 실행전 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)에서 생성 ~ [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 실행 후 종료|
|[정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)|특정 개체에 속하지 않는 변수|`main` 호출 ~ `main` 종료|
|[함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)| 함수가 호출되는 시점에 1회만 생성됨|최초 함수 호출 시점 ~ 소멸 시점은 정의되지 않음|
|[임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)|명시적으로 작성되지 않은 개체|표현식이 종료되면 소멸|

# 지역 변수

함수의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter), 함수내 정의된 변수등 블록 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)에서 사용할 수 있는 변수입니다. 블록의 범위를 벗어나면 자동으로 소멸됩니다.(*해당 변수가 [구조체, 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)라면 자동으로 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출하므로 **자동 변수**라고도 합니다.*)

이러한 자동 소멸은 스마트 포인터(*[auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-unique_ptr/), [shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)나 [Holder](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-holder/)의 개념으로 많이 활용되니 잘 기억해 두시기 바랍니다.

```cpp
void f() {
    int a; // 함수 블록을 벗어나면 자동으로 소멸됩니다.
}
```

# 전역 변수

[전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)는 모든 파일에서 사용할 수 있는 변수입니다. 하지만, [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 도 안되고, 괜히 미리 생성해 두기 때문에 [제로 오버헤드 원칙](https://tango1202.github.io/principle/principle-zero-overhead/)도 위반합니다.

여러 파일에서 사용할 경우에는 다음처럼 `extern`을 사용하여 선언해 줘야 합니다.

```cpp
// global.h에서
extern int g_Val; // 요렇게 선언하면 사용할 수 있습니다.

// global.cpp에서
int g_Val = 10; // 전역 변수 정의

// 여기저기 파일에서
#include "global.h"
```

# 정적 전역 변수

[정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)는 현재 파일에서만 사용 가능한 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98) 입니다. 

```cpp
namespace {
    static int s_Val = 10; // 현재 파일에서 사용 가능  
}
TEST(TestClassicCpp, Static) {
    EXPECT_TRUE(s_Val == 10);
}
```
# 멤버 변수

[구조체, 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/) 등에서 사용하는 변수 입니다. 개체의 데이터를 관리하며, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 본문 실행전 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)에서 생성되고, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 본문 실행 후 소멸됩니다.

```cpp
class Date {
    int m_Year;
    int m_Month;
    int m_Day;
public:
    Date(int year, int month, int day) :
        m_Year(year), // 생성자 본문 실행전 초기화 리스트에서 생성됩니다.
        m_Month(month), 
        m_Day(day) {}
    ~Date() {} // 소멸자 본문 실행 후 소멸됩니다.
};
```

# 정적 멤버 변수

[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)에 `static`을 사용하여 특정 개체에 속하지 않는 [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)를 정의 할 수 있습니다. (*단, [중첩 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A4%91%EC%B2%A9-%ED%81%B4%EB%9E%98%EC%8A%A4)와 [함수 내부의 로컬 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%ED%95%A8%EC%88%98-%EB%82%B4%EB%B6%80%EC%9D%98-%EB%A1%9C%EC%BB%AC-%ED%81%B4%EB%9E%98%EC%8A%A4)는 [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)를 가질 수 없습니다.*) 

선언과 정의의 분리가 필요하기 때문에, 선언 외부에서 초기값을 세팅해야 합니다.(*단, `static const` 인 [정적 상수 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)는 변수 선언에서 초기화를 할 수 있습니다.*)

```cpp
// 선언에서
class T {
public:
    static int s_Val1; // 선언과 정의 분리 필요. 외부에서 초기값이 세팅되어야 함
    // static int s_Val1 = 10; // (X) 컴파일 오류. const만 클래스 선언에서 초기화 지원
    static const int s_Val2 = 20; // const 인 경우 클래스 선언에서 초기화 지원
};

// 선언 외부에서
int T::s_Val1 = 10; // 초기값 세팅

TEST(TestClassicCpp, Static) {
    EXPECT_TRUE(T::s_Val1 == 10);
    EXPECT_TRUE(T::s_Val2 == 20);
}
```

[정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 보다는 선언과 정의를 동시에 하고, 함수 호출시에 1회 생성되는 [함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)를 사용하는게 더 좋습니다.

> *(C++17~) [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, [클래스 정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 정의 및 초기화가 쉬워졌습니다.*

# 함수내 정적 지역 변수

[함수내의 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)는 함수가 호출될 때 최초 1회만 생성되고, 초기화되는 특징을 가지고 있습니다. [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98) 대신 사용하면, 언제 생성되는지 좀더 명시적으로 알 수 있습니다. 또한, 클래스의 [정적 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)와 함께 사용하면, 전역 자원을 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)하기도 좋습니다.

하기 코드를 보면 `GetVal()` 함수를 여러번 호출하더라도 `val` 변수는 1회만 초기화 됩니다. 이 특징을 이용하면, 1회만 생성되는 [Sigleton](https://tango1202.github.io/pattern/pattern-singleton/) 개체를 손쉽게 만들 수 있습니다. 

```cpp
class T {
public:
    static int GetVal() {
        static int s_Val = 30; // 최초 1회 초기화됩니다.
        ++s_Val; // 호출시마다 증가합니다.
        return s_Val;
    }
};

EXPECT_TRUE(T::GetVal() == 31); // 1회 호출
EXPECT_TRUE(T::GetVal() == 32); // 2회 호출
EXPECT_TRUE(T::GetVal() == 33); // 3회 호출
```

# 임시 개체

표현식 내에서 임시적으로 생성되는 개체 입니다. 표현식이 종료되면 함께 소멸됩니다.

다음 코드는 코드를 간결히 작성하기 위해 표현식으로 사용했지만, 내부적으로 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 생성합니다.

```cpp
class T {
public:
    static int f(int x) {
        return x * x; // 리턴시 임시 개체 생성
    }
};

int result = T::f(1 + 1); // 인수를 인자에 전달시 임시 개체 생성
```

는 사실 다음과 같습니다.

```cpp
class T {
public:
    static int f(int x) {
        int temp = x * x; // 임시 개체 생성하여 리턴합니다.
        return temp; 
    }
};

int temp = 1 + 1; // 임시 개체를 생성하여 함수에 전달합니다.
int result = T::f(temp); 
```

이러한 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)는 컴파일러 최적화에 따라, 생성될 수도 있고, 생성되지 않을 수도 있습니다.

따라서, 명시적으로 변수를 만들기 보다는 

```cpp
int temp = 1 + 1; // (△) 비권장. 컴파일러가 최적화를 못할 수도 있습니다.
int result = T::f(temp); 
```

다음처럼 표현식을 그대로 사용하는게 추후 컴파일러 최적화에 도움이 됩니다.

```cpp
int result = T::f(1 + 1); // (O) 컴파일러가 최적화하기 편합니다.
```

또한 [암시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)된다면 불필요하게 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 생성될 수도 있으니, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)은 잘 차단해야 합니다.

> *(C++11~) [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 위해 [우측값 참조(`&&`)](??)가 추가되어 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다.*<br/>
> *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.*


