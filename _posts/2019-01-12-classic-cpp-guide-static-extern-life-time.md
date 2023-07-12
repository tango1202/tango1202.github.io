---
layout: single
title: "#12. [고전 C++ 가이드] 정적 변수와 정적 함수(static), 전역 선언(extern), 개체 수명"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 전역 변수나 정적 전역 변수 보다는 함수내 정적 지역 변수를 사용하라.
> * 정적 멤버 함수는 `obj.f()` 가 아닌 `T::f()` 와 같이 호출하라.
> * 컴파일러 최적화가 쉽도록, RVO가 쉽도록, 임시 개체를 사용하라.

# 개요

|항목|내용|수명|
|--|--|--|
|지역 변수|블록 안에서만 사용 가능한 변수| `{` ~ `}` 범위에서 생성과 소멸|
|전역 변수|모든 파일에서 사용 가능한 변수.<br/>제어가 처음으로 정의를 통과할때 생성됨.(다른 파일 사용시 `extern` 선언 필요)|`main` 호출 ~ `main` 종료|
|정적 전역 변수|현재 파일에서만 사용할 수 있는 전역 변수|`main` 호출 ~ `main` 종료|
|멤버 변수|특정 개체에 속하는 변수|개체 생성시 생성자 실행전 초기화 리스트에서 생성 ~ 소멸자 실행 후 종료|
|정적 멤버 변수|특정 개체에 속하지 않는 변수|`main` 호출 ~ `main` 종료|
|정적 멤버 함수|특정 개체에 속하지 않는 함수|
|함수내 정적 지역 변수| 함수가 호출되는 시점에 1회만 생성됨|최초 함수 호출 시점 ~ 소멸 시점은 정의되지 않음|
|임시 개체|명시적으로 작성되지 않은 개체|표현식이 종료되면 소멸|

# 지역 변수

함수의 인자, 함수내 정의된 변수등 블록 유효 범위에서 사용할 수 있는 변수입니다. 블록의 범위를 벗어나면 자동으로 소멸됩니다.(구조체, 클래스의 경우 자동으로 소멸자를 호출하므로 자동 변수라고도 합니다.)

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

구조체, 클래스 등에서 사용하는 변수 입니다.

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

멤버 변수에 `static` 을 사용하여 특정 개체에 속하지 않는 정적 멤버 변수를 정의 할 수 있습니다. (단, 중첩 클래스와 함수내에 정의된 내부 클래스는 정적 멤버 변수를 가질 수 없습니다.) 

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

멤버 함수에 `static` 을 사용하여 특정 개체에 속하지 않는 정적 함수를 정의 할 수 있습니다.

```cpp
class T {
public:
    static int f() {return 10;} // 정적 함수
};

EXPECT_TRUE(T::f() == 10); // (O) T의 정적 함수 호출
T obj;
EXPECT_TRUE(obj.f() == 10); // (△) 비권장. T의 정적 함수 호출. 되기는 합니다만 일반 멤버 함수 호출과 구분이 안되어 가독성이 떨어집니다.
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

다음 코드는 코드를 간결히 작성하기 위해 표현식으로 사용했지만 내부적으로 임시 개체를 생성합니다.

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

int result = T::f(1 + 1); // (O) 컴파일러가 최적화 시키기 편합니다.
```

또한 암시적으로 형변환된다면 불필요하게 임시 개체가 생성될 수도 있으니, [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)은 잘 차단해야 합니다.

# Return Value Optimization(RVO)

일반적으로 리턴되는 값은 다른 변수에 대입됩니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) :
        m_X(x),
        m_Y(y) {
        std::cout<<"RVO -> T::T()"<<std::endl;
    }
    T(const T& other) {
        std::cout<<"RVO -> T(const T& other)"<<std::endl;    
    }
    T& operator =(const T& other) {
        std::cout<<"RVO -> operator =(const T& other)"<<std::endl;   
        return *this; 
    }
    T f() {
        T result(0, 0);
        return result;
    }
};
T t1(0, 0);
T t2 = t1.f();
```

에서 `T` 객체는 `t1`생성시 1회, `f()`에서 `result`를 생성하면서 2회, `f()`에서 리턴하면서 임시객
체 3회, `t2`를 생성하면서 4회 생성될 것 같지만, 실제 확인해 보면 그렇지 않습니다.

GCC에서는 하기 2개만 실행됩니다.(컴파일러마다 다를 수 있습니다.)

```
RVO -> T::T()
RVO -> T::T()
```

이는 리턴값인 `result`가 `t2`에 대입되므로, 괜히 생성하고 대입하는게 아니라 리턴할 개체를 그냥 `t2`로 사용하기 때문입니다. 이를 Return Value Optimization(RVO) 라고 합니다. 

컴파일러마다 다를 수 있기 때문에, 컴파일러가 최적화를 쉽게 할 수 있도록 임시 개체를 사용하는게 좋습니다.

```cpp
T result(0, 0);
return result; // (△) 비권장. 컴파일러가 최적화를 못할 수도 있습니다.

return result(0, 0); // (O) 임시 개체를 생성하는게 컴파일러가 최적화 시키기 편합니다.
```


