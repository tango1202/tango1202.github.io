---
layout: single
title: "#12. [고전 C++ 가이드] 정적 변수와 정적 함수(static), 전역 선언(extern)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 전역 변수나 정적 전역 변수 보다는 함수내 정적 지역 변수를 사용하라.
> * 정적 멤버 함수는 `T::f()` 와 같이 호출하라.

**개요**

|항목|내용|
|--|--|
|전역 변수|모든 파일에서 사용 가능한 변수. 제어가 처음으로 정의를 통과할때 생성됨.(다른 파일 사용시 `extern` 선언 필요)|
|정적 전역 변수|현재 파일에서만 사용할 수 있는 전역 변수|
|정적 멤버 변수|특정 개체에 속하지 않는 변수|
|정적 멤버 함수|특정 개체에 속하지 않는 함수|
|함수내 정적 지역 변수| 함수가 호출되는 시점에 1회만 생성됨|

**전역 변수**

전역 변수는 모든 파일에서 전방 선언만 하면 사용할 수 있는 변수입니다. 하지만, [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 도 안되고, 괜히 미리 생성되어 [제로 오버헤드 원칙](https://tango1202.github.io/principle/principle-zero-overhead/) 도 위반합니다.

```cpp
// global.h에서
int g_Val = 10; // 전역 변수 정의

// 여기저기 파일에서
#include "global.h"
extern int g_Val; // 요렇게 선언하면 사용할 수 있습니다.
```

**정적 전역 변수**

정적 전역 변수는 현재 파일에서만 사용 가능한 전역 변수 입니다. 

```cpp
namespace {
    static int s_Val = 10; // 현재 파일에서 사용 가능  
}
TEST(TestClassicCpp, Static) {
    EXPECT_TRUE(s_Val == 10);
}
```

**정적 멤버 변수**

하기와 같이 특정 개체에 속하지 않는 정적 멤버 변수를 정의 할 수 있습니다. (단, 함수내에 정의된 중첩 클래스는 정적 멤버 변수를 가질 수 없습니다.) 선언과 정의의 분리가 필요하기 때문에, 외부에서 초기값을 세팅해야 합니다.(단, `static const` 형은 변수 선언에서 초기화를 할 수 있습니다.)

```cpp
// 선언에서
class T {
public:
    static int m_Val1; // 선언과 정의 분리 필요. 외부에서 초기값이 세팅되어야 함
    // static int m_Val1 = 10; // (X) 컴파일 오류. const만 클래스 선언에서 초기화 지원
    static const int m_Val2 = 20; // const 인 경우 클래스 선언에서 초기화 지원
};

// 선언 외부에서
int T::m_Val1 = 10; // 초기값 세팅

TEST(TestClassicCpp, Static) {
    EXPECT_TRUE(T::m_Val1 == 10);
    EXPECT_TRUE(T::m_Val3 == 20);
}
```

**정적 멤버 함수**

하기와 같이 특정 개체에 속하지 않는 정적 함수를 정의 할 수 있습니다.

```cpp
class T {
public:
    static int f() {return 10;} // 정적 함수
};

EXPECT_TRUE(T::f() == 10); // (O) T의 정적 함수 호출
T a;
EXPECT_TRUE(a.f() == 10); // (△) 비권장. T의 정적 함수 호출. 되기는 합니다만 일반 멤버 함수 호출과 구분이 안되어 가독성이 떨어집니다.
```

**함수내 정적 지역 변수**

함수내의 정적 지역 변수는 함수가 호출될 때 최초 1회만 생성되고, 초기화되는 특징을 가지고 있습니다. 전역 변수, 정적 전역 변수 대신 사용하면, 언제 생성되는지 좀더 명시적으로 알 수 있습니다. 또한, 클래스의 멤버 함수에 작성하면, 전역 자원을 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)하기도 좋습니다.

하기 코드를 보면 `GetVal()` 함수를 여러번 호출하더라도 `val` 변수는 1회만 초기화 됩니다. 이 특징을 이용하면, 1회만 생성되는 [Sigleton](https://tango1202.github.io/pattern/pattern-singleton/) 개체를 손쉽게 만들 수 있습니다. 

```cpp
class T {
public:
    static int GetVal() {
        static int val = 30; // 최초 1회 초기화됩니다.
        ++val; // 호출시마다 증가합니다.
        return val;
    }
};

EXPECT_TRUE(T::GetVal() == 31); // 1회 호출
EXPECT_TRUE(T::GetVal() == 32); // 2회 호출
EXPECT_TRUE(T::GetVal() == 33); // 3회 호출
```
