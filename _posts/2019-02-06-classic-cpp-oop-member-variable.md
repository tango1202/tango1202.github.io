---
layout: single
title: "#6. [고전 C++ 개체 지향] 멤버 변수"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 멤버 변수 정의 순서에 따른 용량 최적화

컴파일러는 클래스나 구조체의 멤버 변수를 할당하는데 있어, 메모리 접근 편의를 위해 4byte단위로 멤버 변수를 할당합니다.(이를 패딩(padding)이라 합니다. [`#pragma`](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma) 언급) 

다음 코드는 패딩 작업에 의해 빈공간이 생겨 16byte 크기이지만,

```cpp
class T {
    char m_Char1; // 1byte, 메모리 접근 편의를 위해 32bit(4byte) 단위로 할당(패딩). 3byte 빈공간이 생김 
    int m_Int1; // 4byte
    char m_Char2; // 1byte, 메모리 접근 편의를 위해 32bit(4byte) 단위로 할당(패딩). 3byte 빈공간이 생김 
    int m_Int2; // 4byte
};
EXPECT_TRUE(sizeof(T) == 16);
```

다음 코드는 패딩이 최소화되어 12byte입니다.

```cpp
class T {
    char m_Char1; // 1byte
    char m_Char2; // 1byte, 패딩을 위해 2byte 빈공간이 생김
    int m_Int1; // 4byte
    int m_Int2; // 4byte
};
EXPECT_TRUE(sizeof(T) == 12);      
```

멤버 변수가 4byte 단위로 할당되므로, `char`와 같이 4byte 이하인 멤버 변수들은 몰아서 정의하는게 좋습니다.(`pragma pack`을 이용하면, 데이터 버스 크기를 줄여 메모리 낭비는 줄일 수 있으나, 메모리 접근 속도는 저하됩니다. [`#pragma`](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#pragma) 참고) 

# 멤버 변수 정의 순서와 초기화 리스트 순서

초기화 리스트에 기재된 순서가 아닌, 멤버 변수 정의 순서로 초기화 됩니다. 따라서 헷갈리지 않도록 멤버 변수 정의 순서에 따라 초기화 리스트를 작성하세요.

```cpp
class T {
    int m_A;
    int m_B;
    int m_C;
public:
    T(int a, int b, int c) :
        m_C(c + m_B), // (△) 비권장. 3
        m_B(b + m_A), // (△) 비권장. 2
        m_A(a) {} // (△) 비권장. 1
    int GetA() const {return m_A;}
    int GetB() const {return m_B;}
    int GetC() const {return m_C;}
};
T t(10, 20, 30);
EXPECT_TRUE(t.GetA() == 10 && t.GetB() == 30 && t.GetC() == 60);
```

# 멤버 변수명과 인자명이 같은 경우

멤버 변수명과 인자명이 같더라도 함께 사용할 수 있습니다.

1. 생성자의 초기화 목록에서는 멤버 변수와 인자를 함께 사용할 수 있습니다. `a(a)`로 사용한 경우, 멤버 변수 `a`의 복사 생성자에 인자 `a`를 전달합니다.

2. 함수 본문에서는 인자가 멤버 변수를 가리므로 `this->멤버 변수명`으로 사용해야 합니다.
   
```cpp
class T {
public:
    int a;
    int b;
    int c;
    T(int a, int b, int c) : // 멤버 변수명과 인자명이 같더라도 초기화 리스트에서 사용 가능합니다.
        a(a), 
        b(b),
        c(c) {
        // 함수 본문에서 멤버 변수명과 인자명이 같으면, 멤버 변수는 this를 써서 접근합니다.
        this->a += 1; // 멤버 변수 a를 수정함
        a += 2; // 인자 a를 수정함       
    }
};
T t(10, 20, 30); 
EXPECT_TRUE(t.a == 11 && t.b == 20 && t.c == 30);
```

# 변수 생성과 소멸 시점


정적 멤버 변수 초기화 방법

지역 객체 : { } 안에서 유효

```cpp
void f() {
{
int a; // (O) a = 10; // (O) }a
```
클래스 멤버 : 초기화 리스트 시 생성, 소멸자에서 소멸

```cpp
class Date {
int d, m, y; public: Date(int dd, int mm, int yy) : d(dd), m(mm), y(yy) {
}~Date() {}
}
```

지역 정적 객체 : 최초 함수 호출시 생성. 소멸시점은 정의되지 않음
```cpp
void f() { static int s = 1; // 최초 함수 호출시 생성
s++; }
```
비지역 객체(전역개체, 클래스의 정적 개체) : main 호출시 생성, main 종료시 소멸
```cpp
class Date { static int today; };

// cpp 어딘가에 정의되야 한다. int Date::today = 20; 
```
dll 의 비지역 객체는 dll이 링크될
때 까지 생성되지 않는다. 복잡하므로 지역 정적 개체를 사용
하라.

임시 객체 : 명시적으로 작성되지 않은 객체. 이름없는 객체. 표현식이 종료되면 소멸

# 임시 개체 활용한 최적화, RVO

인자 전달시 임시객체가 생성되지 않도록 하는 것이 좋다. 암시적인 형변환이 되지는 않는지 검토할것. 명시적인 것 보다는 임시 객체를 사용하는 편이 좋다. 언젠간 컴파일러가 최적화 해줄수도 있다.
int i = f(); // 명시적으로 객체 i에 저장
g(i); 보다는
g(f()); // 언젠간 컴파일러가 최적화 할꺼다.

RVO(Return Value Optimization, MEC++ 20항목, 1996년 ISO 제정)

T f() { T t; return t;} void g() {T t = f();} 에서 T 객체는 f()에서 1회, 리턴하면서 임시객
체 1회 g() 함수에서 1회, 3회 생성되고 대입이
1회 이루어진다.

T f() {return T();} // 임시객체로 생성
void g() {T t = f();} 에서 f() 에서 생성한 T 객체를 g() 함수에서
생성한 T객체에 만들어줌