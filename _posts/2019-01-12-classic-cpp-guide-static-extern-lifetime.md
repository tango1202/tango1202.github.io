---
layout: single
title: "#12. [고전 C++ 가이드] 정적 변수와 정적 함수(static), 전역 선언(extern), 개체 수명"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 변수의 수명([유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/))은 짧을 수록 좋다. 최대한 지역 변수로 사용하라.
> * 전역 변수나 정적 전역 변수 보다는 함수내 정적 지역 변수를 사용하라.
> * 정적 멤버 함수는 `obj.f()` 가 아닌 `T::f()` 와 같이 호출하라.
> * 컴파일러 최적화가 쉽도록, 임시 개체를 사용하라.

# 개요

|항목|내용|수명|
|--|--|--|
|지역 변수|블록 안에서만 사용 가능한 변수| `{` ~ `}` 범위에서 생성과 소멸|
|전역 변수|모든 파일에서 사용 가능한 변수.<br/>제어가 처음으로 정의를 통과할때 생성됨.(다른 파일 사용시 `extern` 선언 필요)|`main` 호출 ~ `main` 종료|
|정적 전역 변수|현재 파일에서만 사용할 수 있는 전역 변수|`main` 호출 ~ `main` 종료|
|[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)|특정 개체에 속하는 변수|개체 생성시 생성자 실행전 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)에서 생성 ~ 소멸자 실행 후 종료|
|정적 멤버 변수|특정 개체에 속하지 않는 변수|`main` 호출 ~ `main` 종료|
|정적 멤버 함수|특정 개체에 속하지 않는 함수|
|함수내 정적 지역 변수| 함수가 호출되는 시점에 1회만 생성됨|최초 함수 호출 시점 ~ 소멸 시점은 정의되지 않음|
|임시 개체|명시적으로 작성되지 않은 개체|표현식이 종료되면 소멸|

일반적으로 메모리 공간을 오랫동안 차지하는 전역 변수나 정적 변수보다는 사용하고 빨리 반납하는 지역 변수가 좋습니다. 프로그램 시작과 끝을 관장하면서 필요한 변수가 아니라면 지역 변수로 작성하시기 바랍니다.

# 지역 변수

함수의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter), 함수내 정의된 변수등 블록 유효 범위에서 사용할 수 있는 변수입니다. 블록의 범위를 벗어나면 자동으로 소멸됩니다.([구조체, 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)의 경우 자동으로 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)를 호출하므로 **자동 변수**라고도 합니다.)

```cpp
void f() {
    int a; // 함수 블록을 벗어나면 자동으로 소멸됩니다.
}
```

# 전역 변수

전역 변수는 모든 파일에서 사용할 수 있는 변수입니다. 하지만, [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 도 안되고, 괜히 미리 생성되어 [제로 오버헤드 원칙](https://tango1202.github.io/principle/principle-zero-overhead/) 도 위반합니다.

```cpp
// global.h에서
int g_Val = 10; // 전역 변수 정의

// 여기저기 파일에서
#include "global.h"
extern int g_Val; // 요렇게 선언하면 사용할 수 있습니다.
```

# 정적 전역 변수

정적 전역 변수는 현재 파일에서만 사용 가능한 전역 변수 입니다. 

```cpp
namespace {
    static int s_Val = 10; // 현재 파일에서 사용 가능  
}
TEST(TestClassicCpp, Static) {
    EXPECT_TRUE(s_Val == 10);
}
```
# 멤버 변수

[구조체, 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/) 등에서 사용하는 변수 입니다. 개체의 데이터를 관리하며, [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/) 본문 실행전 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)에서 생성되고, [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/) 본문 실행 후 소멸됩니다.

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

[멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)에 `static` 을 사용하여 특정 개체에 속하지 않는 정적 멤버 변수를 정의 할 수 있습니다. (단, [중첩 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EC%A4%91%EC%B2%A9-%ED%81%B4%EB%9E%98%EC%8A%A4)와 [함수 내부의 로컬 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%ED%95%A8%EC%88%98-%EB%82%B4%EB%B6%80%EC%9D%98-%EB%A1%9C%EC%BB%AC-%ED%81%B4%EB%9E%98%EC%8A%A4)는 정적 멤버 변수를 가질 수 없습니다.) 

선언과 정의의 분리가 필요하기 때문에, 선언 외부에서 초기값을 세팅해야 합니다.(단, `static const` 형은 변수 선언에서 초기화를 할 수 있습니다.)

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

# 정적 멤버 함수

[멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)에 `static` 을 사용하여 특정 개체에 속하지 않는 정적 멤버 함수를 정의 할 수 있습니다.

```cpp
class T {
public:
    static int f() {return 10;} // 정적 멤버 함수
};

EXPECT_TRUE(T::f() == 10); // (O) T의 정적 멤버 함수 호출
T obj;
EXPECT_TRUE(obj.f() == 10); // (△) 비권장. T의 정적 멤버 함수 호출. 되기는 합니다만 일반 멤버 함수 호출과 구분이 안되어 가독성이 떨어집니다.
```

# 함수내 정적 지역 변수

함수내의 정적 지역 변수는 함수가 호출될 때 최초 1회만 생성되고, 초기화되는 특징을 가지고 있습니다. 전역 변수, 정적 전역 변수 대신 사용하면, 언제 생성되는지 좀더 명시적으로 알 수 있습니다. 또한, 클래스의 정적 멤버 함수에 작성하면, 전역 자원을 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)하기도 좋습니다.

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

다음 코드는 코드를 간결히 작성하기 위해 표현식으로 사용했지만, 내부적으로 임시 개체를 생성합니다.

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

이러한 임시 개체는 컴파일러 최적화에 따라, 생성되지 않을 수도 있고 생성될 수도 있습니다.

따라서, 명시적으로 변수를 만들기 보다는 표현식을 그대로 사용하는게 추후 컴파일러가 최적화하는데 도움이 됩니다.

```cpp
int temp = 1 + 1; // (△) 비권장. 컴파일러가 최적화를 못할 수도 있습니다.
int result = T::f(temp); 

int result = T::f(1 + 1); // (O) 컴파일러가 최적화하기 편합니다.
```

또한 암시적으로 형변환된다면 불필요하게 임시 개체가 생성될 수도 있으니, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)은 잘 차단해야 합니다.



