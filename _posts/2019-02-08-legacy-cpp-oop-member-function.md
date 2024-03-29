---
layout: single
title: "#8. [레거시 C++ 개체 지향] 멤버 함수, 상수 멤버 함수, 가상 함수, Getter, Setter"
categories: "legacy-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 수정하지 않는다면, [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)로 작성하라.
> * [정적 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 `obj.f()` 가 아닌 `T::f()` 와 같이 호출하라.
> * 자식 개체에서 부모 개체의 비 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 재정의 하지 마라.
> * [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 정의하면 [가상 함수 테이블](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)을 위한 추가 공간이 필요하니 꼭 필요한 경우만 사용하라.
> * [Getter 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98)의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)은 기본 자료형인 경우 값 복사로, 클래스 타입인 경우 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 작성하라.
> * [Setter 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)의 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 [기본 타입](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/)인 경우 값 복사로, 클래스 타입인 경우 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 작성하라.

> **모던 C++**
> * (C++11~) [override](https://tango1202.github.io/cpp/modern-cpp-class/#override)가 추가되어 [가상 함수 오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9)의 코딩 규약이 좀더 단단해졌습니다.
> * (C++11~) [final](https://tango1202.github.io/cpp/modern-cpp-class/#final)이 추가되어 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 더이상 [오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 못하게 할 수 있습니다.
> * (C++11~) [멤버 함수 참조 지정자](https://tango1202.github.io/cpp/modern-cpp-member-function-ref/)가 추가되어 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)에 `&`, `&&` 로 [좌측값](https://tango1202.github.io/cpp/modern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)에서 호출될때와 [우측값](https://tango1202.github.io/cpp/modern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)에서 호출될 때를 구분하여 [함수 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 할 수 있습니다.
 
**개요**

[멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 다음과 같은 것이 있습니다.

이중 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)를 정의하지 않으면, 이들을 사용할때 컴파일러가 암시적으로 정의합니다.(*[클래스의 암시적 정의](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-implicit-definition/) 참고*)

|항목|내용|
|--|--|
|`T() {}`|[기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)|
|`T(const T& other) {}`|[복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|
|`~T() {}`|[소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)|
|`T& operator =(const T& other) {}`|[복사 대입 연산자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|
|`operator U() const {}`|[형변환 연산자 정의](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)|
|`T& operator +=(const T& other) {}`| [연산자 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)|
|`U f() {}`|[멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)|
|`U f() const {}`|[상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)|
|`static U f() {}`|[정적 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)|
|`virtual U f() {}`|[가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)|
|`virtual U f() = 0;`|[순가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)|

# 멤버 함수

클래스는 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 위해 데이터(*[멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)*)와 이를 처리하는 함수(*[멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)*)로 응집합니다.

다음 예에서 `Date`클래스는 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)인 `m_Year`, `m_Month`, `m_Day`과 이를 처리하는 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)들로 구성되어 있는데요, `m_Year`, `m_Month`, `m_Day`는 `private`여서 직접 접근할 수 없으며, [Getter 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98)와 [Setter 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)를 통해 은닉되어 있습니다.

```cpp
class Date {
    int m_Year;
    int m_Month;
    int m_Day;
public: 
    Date(int year, int month, int day) :
        m_Year(year),
        m_Month(month), 
        m_Day(day) {}

    // Getter/Setter
    int GetYear() const {return m_Year;} // 상수 멤버 함수
    int GetMonth() const {return m_Month;}
    int GetDay() const {return m_Day;}

    void SetYear(int val) {m_Year = val;} // 멤버 함수
    void SetMonth(int val) {m_Month = val;}
    void SetDay(int val) {m_Day = val;}

    // 내부적으로 전체 개월수를 계산하기 위해,
    // 데이터와 처리하는 함수를 응집하였습니다. 
    int CalcTotalMonth() const { // 상수 멤버 함수
        return m_Year * 12 + m_Month; 
    }
};
Date date(20, 2, 10); // 20년 2월 10일
EXPECT_TRUE(date.CalcTotalMonth() == 20 * 12 + 2); 
```

# 상수 멤버 함수

[멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)의 뒤에 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 붙여 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 만들 수 있습니다. [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)는 [상수성 계약](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)에 따라 다음을 준수합니다.

1. [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 수정하지 않습니다.
    
    ```cpp
    class T {
        int m_Val;
    public:
        void Func() const {m_Val = 10;} // (X) 컴파일 오류. const 함수는 멤버 변수를 수정할 수 없습니다.
    };
    ```

2. [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 몰래 수정할 수 있는 [포인터나 참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/)를 리턴하지 않습니다.
   
    ```cpp
    class T {
        int m_Val;
    public:
        int* Func() const {return &m_Val;} // (X) 컴파일 오류. int* 리턴. const 함수는 const int*를 리턴해야 합니다.
    };
    ```

3. 내부 구현시 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)만을 호출합니다.
 
    ```cpp
    class T {
        int m_Val;
    public:
        void Func() const { NonConstFunc();} // (X) 컴파일 오류. const 함수는 비 상수 멤버 함수 호출할 수 없습니다.
        void NonConstFunc() {}
    };
    ```   

4. 메모리를 수정하지 않기 때문에 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)하지 않습니다.

따라서 [상수성](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)을 잘 지키면, [예외에 안전](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-safe/)하며, [상수성 계약](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)에 의해 안전하게 코딩할 수 있습니다. 함수가 개체를 변경시키는지
아닌지 항상 분명하게 인지하고, 최대한 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)로 작성하세요.

# 비 상수 멤버 함수의 비 상수성 전파

[상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 될 수 있음에도 [비 상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%B9%84-%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98%EC%9D%98-%EB%B9%84-%EC%83%81%EC%88%98%EC%84%B1-%EC%A0%84%ED%8C%8C)로 작성한다면, 이를 사용하는 모든 함수나 개체들이 비 상수로 만들어져야 합니다. 비 상수성은 바이러스처럼 전파되니 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 될 수 있다면 꼭 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)로 만드세요.

```cpp
class T {
    int m_Val;
public:
    int GetVal() {return m_Val;} // (△) 비권장. 상수 멤버 함수인데 비 상수로 정의했습니다.
};

class U {
    T m_T;
public:
    // m_T.GetVal()은 비 상수 멤버 함수여서 상수 멤버 함수인 GetInnerVal()에서 호출할 수 없습니다.
    // 어쩔 수 없이 GetInnerVal()을 비 상수로 만들어야 컴파일 할 수 있습니다.
    int GetInnerVal() const {return m_T.GetVal();} // (X) 컴파일 오류. 
};
```

# 정적 멤버 함수

[멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)에 `static`을 사용하여 개체에 속하지 않는 [정적 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)를 정의 할 수 있습니다.

```cpp
class T {
public:
    static int f() {return 10;} // 정적 멤버 함수
};

EXPECT_TRUE(T::f() == 10); // (O) T의 정적 멤버 함수 호출
T obj;
EXPECT_TRUE(obj.f() == 10); // (△) 비권장. T의 정적 멤버 함수 호출. 되기는 합니다만 일반 멤버 함수 호출과 구분이 안되어 가독성이 떨어집니다.
```

# 가상 함수

`virtual`을 붙이면 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 되며, 부모 개체의 [포인터나 참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/)로 자식 개체에서 재구현한 함수(*이를 [오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9)(overriding)이라 합니다.*)에 접근할 수 있습니다. 

다음 코드에서 일반 함수인 `f()`와 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)인 `v()`를 `Derived`에서 재구현 했을때, 어떻게 동작하는지 테스트하였습니다.

동일한 개체를 

* `Base` 포인터로 일반 함수인 `f()`를 호출하면, `Base::f()`가 호출되고, 
* 자식 개체인 `Derived` 포인터로 호출하면, `Derived::f()`가 호출됩니다. 

일관성이 없으므로 사용하지 말아야 합니다.

[가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)인 경우는 `Base` 포인터 이던 `Derived` 포인터 이던, `Derived::v()`가 정상적으로 호출됩니다.

```cpp
class Base {
public:
    int f() {return 10;}
    virtual int v() {return 10;} // 가상 함수
};

class Derived : public Base {
public:
    int f() {return 20;} // (△) 비권장. Base의 동일한 이름의 비 가상 함수를 가림
    virtual int v() {return 20;} // (O) Base의 가상 함수 재구현(오버라이딩)  굳이 virtual을 붙일 필요는 없습니다.
};

Derived d;
Base* b = &d;

EXPECT_TRUE(b->f() == 10); // (△) 비권장. Base 개체를 이용하면 Base::f()가 호출됨 
EXPECT_TRUE(d.f() == 20); // (△) 비권장. Derived 개체를 이용하면 Derived::f()가 호출됨        
EXPECT_TRUE(static_cast<Base&>(d).f() == 10); // (△) 비권장. 가려진 Base::f() 함수를 호출

EXPECT_TRUE(b->v() == 20); // (O)가상 함수여서 Derived::v()가 호출됨
EXPECT_TRUE(d.v() == 20); // (O)가상 함수여서 Derived::v()가 호출됨
```

자식 개체에서 부모 개체의 함수를 [오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 하려면,

1. 부모 개체에서 `virtual` 함수로 선언해야 합니다.
1. 자식 개체에서 함수명/[인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입/[상수 멤버 함수의 const](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)/[동적 예외 사양](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)이 동일해야 합니다. 만일 다르다면, [오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 되지 않습니다. 컴파일러가 오류를 잘 감지하지 못하니 주의하시기 바랍니다.

> *(C++11~) [override](https://tango1202.github.io/cpp/modern-cpp-class/#override)가 추가되어 [가상 함수 오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9)의 코딩 규약이 좀더 단단해졌습니다. 또한, [final](https://tango1202.github.io/cpp/modern-cpp-class/#final)이 추가되어 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 더이상 [오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 못하게 할 수 있습니다.*

**리턴값 변경**
 
자식 개체에서 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 재구현시 리턴 타입은 바뀔 수도 있습니다. 부모 개체의 것과 같거나 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/) 관계(*공변, covariant*)이면 됩니다.

```cpp
class Base {
public:
    virtual Base* v() {return this;} // 가상 함수
};

class Derived : public Base {
public:
    virtual Derived* v() {return this;} // (O) Derived 는 Base와 상속 관계여서 가능
    // virtual int* v() {return NULL;} // (X) 컴파일 오류. 밑도 끝도 없는 타입은 안됨
};
```

# 가상 함수 테이블(Virtual Function Table, vTable)

부모 클래스에 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있다면, 내부적으로 해당 개체의 시작 주소에 [가상 함수 테이블](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)(*[가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 포인터에 대한 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)*)을 생성합니다. 컴파일러에 따라 다를 수도 있으나 대부분 8byte 입니다. [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98) 호출시에는 [가상 함수 테이블](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)을 참조하여 호출하게 됩니다.

```cpp
class Base {
public: 
    virtual void v1() {}
    virtual void v2() {}
    virtual void v3() {}
    void f();
};
class Derived : public Base {
    virtual void v2() {} // 오버라이딩
    virtual void v3() {} // 오버라이딩
};
```

상기와 같이 `v2()`, `v3()`를 [오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 했다면, [가상 함수 테이블](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)은 다음과 같이 구성됩니다.

1. `f()`는 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 아니므로 [가상 함수 테이블](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)에 포함되지 않습니다.
2. `v1()`은 [오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 하지 않았으므로, `Derived`의 [가상 함수 테이블](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)에 `Base::v1()`의 주소가 저장됩니다.
3. `v2()`, `v3()`은 [오버라이딩](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EB%B6%80%EB%AA%A8-%EA%B0%9C%EC%B2%B4%EC%9D%98-%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9) 되었으므로, `Derived`의 [가상 함수 테이블](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)에 `Derived::v2()`, `Derived::v3()`의 주소가 저장됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/80878e38-22ff-4799-b3ad-ddc5e33829d8)

따라서, 

```cpp
Derived d;
Base* b = &d;
b->v3();
```

와 같이 `Base` 포인터로 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)인 `v3()`을 호출하면, 다음 경로에 따라 `Derived::v3()` 이 호출됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/bab05099-af07-4661-bf93-6d348353367f)

[가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 정의된 개체는 [가상 함수 테이블](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98-%ED%85%8C%EC%9D%B4%EB%B8%94virtual-function-table-vtable)의 추가 공간을 필요로 하므로, 불필요하게 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 정의하면 메모리를 낭비하게 됩니다. [인터페이스](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-abstract-class-interface/#%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)가 필요하거나, [다형적](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-polymorphism/) 동작이 필요한 경우만 사용하시기 바랍니다.

# 순가상 함수

[순가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)는 실제 구현없이 함수 규약만 정의할때 사용합니다. [순가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%88%9C%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 있는 클래스는 인스턴스화 할 수 없으며, 반드시 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)해서 자식 개체에서 구현해야 합니다.

```cpp
class IEatable {
public:
    virtual void Eat() = 0; // 순가상 함수
};

class Dog : public IEatable {
public:        
    virtual void Eat() {} // 순가상 함수는 자식 개체에서 실제 구현을 해야 합니다.
};
IEatable eatable; // (X) 컴파일 오류. 순가상 함수가 있기 때문에 인스턴스화 할 수 없습니다.
Dog dog; // (O)
```

# Getter 함수

개체의 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 리턴하는 함수를 특별히 [Getter 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#getter-%ED%95%A8%EC%88%98)라고 합니다. 다음 규칙에 맞춰 [리턴 타입](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 작성하는게 좋습니다.

1. `int` 등 기본 자료형의 경우는 복사 부하가 참조 부하보다 적기 때문에 값을 리턴하는게 좋습니다. 
2. 클래스 등 복사 부하가 참조 부하보다 큰 개체는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴하는게 좋습니다.
3. [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)는 [널 값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)이 되는 경우가 없기 때문에 포인터보다는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 리턴하는게 좋습니다.
4. [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 수정하지 않는다면 [상수 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)로 작성합니다.
5. 값 타입을 리턴하는 경우는 어짜피 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)이 복제되므로, [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)에 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 굳이 붙일 필요가 없습니다. 

```cpp
class T {};
class U {
    int m_Val1;
    T m_Val2;
public:
    int GetVal1() const {return m_Val1;} // (O) 멤버 변수의 값을 리턴
    int& GetVal1() {return m_Val1;} // (O) 멤버 변수의 값을 수정하는 참조자를 리턴    

    const T& GetVal2() const {return m_Val2;} // (O) 멤버 변수의 참조자 리턴
    T& GetVal2() {return m_Val2;} // (O) 멤버 변수의 값을 수정하는 참조자를 리턴  

    // const int GetVal1() const {return m_Val1;} // (△) 비권장. 리턴값이 기본 타입이라면 어짜피 리턴값이 복제되므로, `const` 부적절
    // int GetVal1() {return m_Val1;} // (△) 비권장. 멤버 변수를 수정하지 않으므로 상수 함수가 적절
    // const int GetVal1() {return m_Val1;} // (△) 비권장. 멤버 변수를 수정하지 않으므로 상수 함수가 적절

    // (△) 비권장. 포인터 보다는 참조자가 적절
    // const int* GetVal1() const {return &m_Val1;} // (△) 비권장. 포인터 보다는 참조자가 적절
    // int* GetVal1() {return &m_Val1;} // (△) 비권장. 포인터 보다는 참조자가 적절
    // int* GetVal1() const {return &m_Val1;} // (X) 컴파일 오류. 상수성 계약 위반
    // const int* GetVal1() {return &m_Val1;} // (△) 비권장. 상수 함수가 적절 

    // const int& GetVal1() const {return m_Val1;} // (△) 비권장. int는 기본 자료형이어서 참조 보다는 값 전달이 적절
    // int& GetVal1() const {return m_Val1;} // (X) 컴파일 오류. 상수성 계약 위반
    // const int& GetVal1() {return m_Val1;} // (△) 비권장. 상수 함수가 적절

    // (△) 비권장. 복사 부하가 참조 부하보다 큰 개체여서 값 복사 보다는 참조가 적절
    // const T GetVal2() const {return m_Val2;} // (△) 비권장. 값 복사 보다는 참조가 적절. 어짜피 리턴값이 복제되므로, `const` 부적절
    // T GetVal2() const {return m_Val2;} // (△) 비권장. 값 복사 보다는 참조가 적절
    // T GetVal2() {return m_Val2;} // (△) 비권장. 값 복사 보다는 참조가 적절. 멤버 변수를 수정하지 않으므로 상수 함수가 적절
    // const T GetVal2() {return m_Val2;} // (△) 비권장. 값 복사 보다는 참조가 적절. 어짜피 리턴값이 복제되므로, `const` 부적절, 멤버 변수를 수정하지 않으므로 상수 함수가 적절

    // (△) 비권장. 포인터 보다는 참조자가 적절
    // const T* GetVal2() const {return &m_Val2;} // (△) 비권장. 포인터 보다는 참조자가 적절
    // T* GetVal2() {return &m_Val2;} // (△) 비권장. 포인터 보다는 참조자가 적절
    // T* GetVal2() const {return &m_Val2;} // (X) 컴파일 오류. 상수성 계약 위반
    // const T* GetVal2() {return &m_Val2;} // (△) 비권장. 상수 함수가 적절

    // T& GetVal2() const {return m_Val2;} // (X) 컴파일 오류. 상수성 계약 위반
    // const T& GetVal2() {return m_Val2;} // (△) 비권장. 상수 함수가 적절
};
```

# Setter 함수

개체의 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)를 설정하는 함수를 특별히 [Setter 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#setter-%ED%95%A8%EC%88%98)라고 합니다. 다음 규칙에 맞춰 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 작성하는게 좋습니다.

1. `int` 등 기본 자료형의 경우는 복사 부하가 참조 부하보다 적기 때문에 값을 전달하는게 좋습니다. 
2. 클래스 등 복사 부하가 참조 부하보다 큰 개체는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 전달하는게 좋습니다.
    * [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 수정하는게 아니기 때문에 [함수 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 상수여야 합니다.
3. 널검사가 최소화 될 수 있도록, [널 값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EB%84%90-%ED%8F%AC%EC%9D%B8%ED%84%B0)이 되지 않는 경우라면 포인터보다는 [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 전달하는게 좋습니다.
4. 값 타입을 전달하는 경우는 어짜피 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 복제되므로, 굳이 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)에 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)를 붙일 필요가 없습니다. 

```cpp
class T {};
class U {
    int m_Val1;
    T m_Val2;
public:
    void SetVal1(int val) {m_Val1 = val;} // (O) 멤버 변수에 값 대입
    void SetVal2(const T& val) {m_Val2 = val;} // (O) 참조자를 통해 전달받은 인자를 현 멤버 변수에 복사

    // void SetVal1(const int val) {m_Val1 = val1;} // (△) 비권장. 인자에 복사되므로 int와 const int는 동일 취급됨
    // void SetVal1(int* val) {m_Val = *val;} // (△) 비권장. 인자는 상수 타입이어야 함
    // void SetVal1(const int* val) {m_Val = *val;} // (△) 비권장. 포인터보다는 참조자가 좋음
    // void SetVal1(int& val) {m_Val = *val;} // (△) 비권장. 인자는 상수 타입이어야 함
    // void SetVal1(const int& val) {m_Val = *val;} // (△) 비권장. 기본 자료형의 경우 값 복사가 좋음

    // void SetVal2(T val) {m_Val2 = val;} // (△) 비권장. 값 복사 보다는 참조가 적절
    // void SetVal2(const T val) {m_Val2 = val;} // (△) 비권장. 인자에 복사되므로 T와 const T는 동일 취급됨

    // void SetVal2(T* val) {m_Val2 = *val;} // (△) 비권장. 인자는 상수 타입이어야 함
    // void SetVal2(const T* val) {m_Val2 = *val;} // (△) 비권장. 포인터보다는 참조자가 좋음
    // void SetVal2(T& val) {m_Val2 = val;} // (△) 비권장. 인자는 상수 타입이어야 함
};
```
