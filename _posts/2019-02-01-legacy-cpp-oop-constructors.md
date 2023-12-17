---
layout: single
title: "#1. [레거시 C++ 개체 지향] 생성자"
categories: "legacy-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)가 필요하다면 명시적으로 구현하고, 필요없다면 못쓰게 만들어라.
> * [값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)에서는 필요한 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 모두 나열하고 초기화하라. 
> * [인자가 1개인 값 생성자(형변환 생성자)](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90)는 [explicit](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)로 [암시적 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하라.
> * [암시적 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 정상 동작하도록 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/) 정의시 스마트 포인터(*[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 사용하고, 필요없다면 못쓰게 만들어라.
> * [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)에서 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하지 마라.
> * [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/) 전용 기반 클래스는 [protected 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%83%81%EC%86%8D-%EC%A0%84%EC%9A%A9-%EA%B8%B0%EB%B0%98-%ED%81%B4%EB%9E%98%EC%8A%A4---protected-%EC%83%9D%EC%84%B1%EC%9E%90)로 만들어라.

> **모던 C++**
> * (C++11~) [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)을 위해 [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [임시 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다.
> * (C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)를 제공하여 클래스인지, [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)인지, 구조체인지 구분없이 중괄호(`{}`)를 이용하여 일관성 있게 초기화 할 수 있으며, [초기화 파싱 오류](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98)도 해결했습니다.
> * (C++11~) [default, delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#default%EC%99%80-delete)가 추가되어 [암시적으로 생성되는 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-implicit-definition/)의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.
> * (C++11~) [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#final)이 추가되어 강제적으로 [상속을 제한](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C)할 수 있습니다.
> * (C++11~) [생성자 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84)이 추가되어 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)의 [초기화 리스트](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 코드가 좀더 간결해 졌습니다.
 
# 개요

[생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)는 개체가 생성될 때 제일 먼저 호출되는 특수 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)입니다. 개체가 메모리에 할당된 뒤에 호출되고, 초기값을 설정하는 역할을 합니다.

좋은 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)는 [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)에 언급되었듯, ***잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게*** 구현해야 하는데요, 그러기 위해선 [명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 필요한 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)는 모두 나열하는게 좋습니다.

또한, 암시적으로 은근슬쩍 동작하는건 사용하지 않는다면 차단하고(*[암시적 정의 차단](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-complete-class/#%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98-%EC%B0%A8%EB%8B%A8) 참고*), [복사 생성](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)이 올바르게 작동하도록 신경써줘야 합니다.

|항목|내용|
|--|--|
|`T() {}`|[기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)|
|`T(int, int) {}`|[값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)|
|`T(int) {}`|[인자가 1개인 값 생성자(형변환 생성자)](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90)|
|`T(const T& other) {}`|[복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|

# 기본 생성자

인수없는 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)를 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)라고 합니다. 아무런 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)없이 생성할때 기본적인 초기값으로 개체를 생성해 줍니다.

`T t;`로 호출할 수 있습니다.(*`T t();` 와 같이 하면 `T`를 리턴하는 함수 `t()`선언입니다. [초기화 파싱 오류](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98) 참고*)

```cpp
class T {
public:
    T() {}
};
T t; // (O) 개체 정의(인스턴스화)
T t(); // (X) T를 리턴하는 함수 t() 선언
```
> *(C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94)를 제공하여 클래스인지, [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/)인지, 구조체인지 구분없이 중괄호(`{}`)를 이용하여 일관성 있게 초기화 할 수 있으며, [초기화 파싱 오류](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98)도 해결했습니다.*

# 암시적 기본 생성자

컴파일러는 다른 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)(*[값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)던, [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)던*)가 정의되지 않으면, [암시적으로 기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 정의해 줍니다.

```cpp
class T1 {
};
T1 t1; // (O) 암시적 기본 생성자 사용

class T2 {
public:
    T2() {} // 사용자 정의 기본 생성자가 있음
};
T2 t2; // (O) 사용자가 정의한 기본 생성자 사용

class T3 {
public:
    T3(int, int) {} // 값 생성자가 있어 암시적 기본 생성자가 정의되지 않음
};
T3 t3; // (X) 컴파일 오류. 기본 생성자 정의 안됨

class T4 {
public:
    T4(const T4& other) {*this = other;} // 복사 생성자가 있어 암시적 기본 생성자가 정의되지 않음
};
T4 t4; // (X) 컴파일 오류. 기본 생성자 정의 안됨
```

[암시적 기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)에서는 [자동 제로 초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 수행하기 때문에 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)의 메모리 영역이 제로(`0`)로 초기화 됩니다.

하지만 안타깝게도 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 어떻게 호출하느냐에 따라 [자동 제로 초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94) 적용 여부가 달라 집니다.

|항목|내용|
|--|--|
|`T t;`나 `T* ptr = new T;`|괄호 없이 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하면 [자동 제로 초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 하지 않습니다.|
|`T* ptr = new T();`| 괄호를 사용하여 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하면 [자동 제로 초기화](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94)를 합니다.|

```cpp
class T {
    int m_Val; // (△) 암시적으로 기본 생성자가 자동 제로 초기화되거나 안되거나 할 수 있습니다.
public:
    int GetVal() const {return m_Val;}
};
T t1; // (O) 컴파일러가 암시적으로 정의한 기본 생성자

EXPECT_TRUE(t1.GetVal() == 0 || t1.GetVal() != 0); // 0으로 자동 초기화 되거나 안될 수 있습니다.

T t2 = T(); // 자동 제로 초기화 됩니다. T t2();는 T를 리턴하는 함수 선언이어서(초기화 파싱 오류) T t2 = T();와 같이 초기화 합니다.
EXPECT_TRUE(t2.GetVal() == 0);
```

그리고, [참조자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90) 형식이나 [const](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/)로 선언된 [상수 멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)는 생성시 초기값이 전달되야 하기 때문에, [암시적 기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)로 초기화 할 수 없으며, 명시적으로 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)를 구현해야 합니다.

```cpp
class T1 {
private:
    const int& m_Val; // 멤버 변수에 참조자가 있어, 암시적으로 생성한 기본 생성자에서 초기화 할 수 없음
};
T1 t1; // (X) 컴파일 오류. 기본 생성자에서 멤버 변수 초기화 안됨

class T2 {
    const int m_Val; // 멤버 변수에 상수형 개체가 있어, 암시적으로 생성한 기본 생성자에서 초기화 할 수 없음
};
T2 t2; // (X) 컴파일 오류. 기본 생성자에서 멤버 변수 초기화 안됨
```

# 명시적 기본 생성자

[기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)가 필요하다면, [암시적인 기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 활용하기 보다는 명시적으로 정의해서 사용하는 편이 유지보수 측면에서 좋습니다. [명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 필요한 모든 요소를 나열하고 초기화 하는게 **코딩 계약**상 좋거든요.

```cpp
class T {
    int m_Val;
}
T t; // (△) 비권장. 암시적 기본 생성자 사용
```

보다는, [기본값 인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EA%B8%B0%EB%B3%B8%EA%B0%92-%EC%9D%B8%EC%9E%90)를 사용하여,

```cpp
class T {
    int m_Val;
public:
    explicit T(int val = 0) : // 값 생성자의 기본값을 이용해 암시적 기본 생성자를 없앴습니다.
        m_Val(val) {}
};
T t1; // (O) 기본값으로 값 생성자 호출
T t2(0); // (O) 기본값과 동일한 값으로 값 생성자 호출
T t3(10); // (O) 임의 값으로 값 생성자 호출
```
가 낫습니다.

# 값 생성자

[값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)는 전달한 인수들로 개체를 생성시킵니다.

[값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90) 구현은 

1. [명시적 의존성 원칙](https://tango1202.github.io/principle/principle-explicit-dependencies/)에 따라 필요한 모든 요소를 나열하고 초기화하는게 **코딩 계약**상 좋고,
2. 불필요한 대입의 오버헤드를 줄이기 위해 [초기화 리스트](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)를 이용하는게 좋습니다.(*[초기화 리스트](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 참고*)

```cpp
class T {
private:
    int m_X;
    int m_Y;
public:
    T(int x, int y) : // 필요한 모든 인자를 나열
        m_X(x), // 초기화 리스트로 모든 멤버 변수 초기화
        m_Y(y) {}
};
T t(10, 20); // (O) 
```

# 형변환 생성자

특별히 [값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)에 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 1개만 있으면, [암시적인 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 하므로 [형변환 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90)라고도 합니다. [암시적 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)은 코딩 계약을 망쳐버리고, 코드 분석도 어렵게 만드니 [명시적 변환 생성 지정자(explicit)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)를 사용해서 차단하는게 좋습니다.(*[명시적 변환 생성 지정자(explicit)](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit) 참고*)

```cpp
class T {
    int m_Val;
public:
    explicit T(int val) {} // 꼭 explicit를 사용하세요.  
};
```

# 복사 생성자

동일한 타입 1개를 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달 받는 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)를 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)라고 합니다. [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)는 전달받은 개체를 [this](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-struct-class-union/#this-%ED%8F%AC%EC%9D%B8%ED%84%B0)에 복제하는 역할을 합니다.

```cpp
class T {
public:
    T(const T& other) { // other는 수정되지 않으므로 const 형입니다. 불필요한 복제가 없도록 &를 사용합니다.
        // other를 this에 복제해야 합니다.
    }
};
```

# 암시적 복사 생성자

[복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 정의하지 않으면, 컴파일러는 [암시적으로 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 정의해 줍니다. 기본 동작은 멤버별 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 호출입니다.

```cpp
class T {
    int m_X;
    int m_Y;
public:
    T(int x, int y) : 
        m_X(x), 
        m_Y(y) {} 
    // 암시적 복사 생성자의 기본 동작은 멤버별 복사 생성자 호출입니다.    
    // T(const T& other) :
    //     m_X(other.m_X),
    //     m_Y(other.m_Y) {}
    int GetX() const {return m_X;}
    int GetY() const {return m_Y;}
};
T t1(10, 20);
T t2 = t1; // (O) 타입이 같다면 복사 생성자 호출
T t3(t1); // (O) 명시적으로 복사 생성자 호출

EXPECT_TRUE(t2.GetX() == 10 && t2.GetY() == 20);
EXPECT_TRUE(t3.GetX() == 10 && t3.GetY() == 20);
```

# 포인터 멤버 변수의 소유권 분쟁

[new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 생성한 것은 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 소멸(*[힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 참고*) 시켜야 합니다. 그렇지 않으면 메모리 릭이 발생합니다. 그렇다고 여러 차례 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 한다면 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다.(*[delete 여러번 호출 금지](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#delete-%EC%97%AC%EB%9F%AC%EB%B2%88-%ED%98%B8%EC%B6%9C-%EA%B8%88%EC%A7%80) 참고*) [new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 생성한 것은 단 한번만 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 해야 합니다.

예를 들어 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)에서 [new](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 생성한 [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 개체를 전달받고, 안전한 소멸을 보장하기 위해 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)로 소멸시키는 `T`개체를 정의한다고 합시다.

```cpp
class T {
    int* m_Val;
public:
    // val : new 로 생성된 것을 전달하세요.
    explicit T(int* val) :
        m_Val(val) {}
    // 암시적 복사 생성자의 기본 동작은 멤버별 복사 생성자 호출입니다.    
    // T(const T& other) : 
    //     m_Val(other.m_Val) {} // (△) 비권장. 동일한 힙 개체를 참조합니다.        
    
    // 힙 개체를 메모리에서 제거 합니다.
    ~T() {delete m_Val;} 
};        
```

실행을 시켜보면 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다.

```cpp
// (X) 예외 발생. t1이 delete 한 것을 t2도 delete 합니다.
{
    T t1(new int(10));
    T t2(t1); // 복사 생성의 결과 t1과 t2가 동일한 힙 개체를 참조합니다.
}
```

`T t2(t1);`은 [암시적 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하는데요, `other.m_Val` 값이  `m_Val`에 그대로 복사되어 같은 곳을 가리키게 됩니다. 따라서 다음 그림처럼 `t1`, `t2` 가 동일한 [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 개체를 참조하게 되죠.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/16376f0c-e8b0-4eb5-b5a0-f28fc5a9cc43)

`t1`과 `t2` 의 [유효 범위](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-scope/)가 끝나서 각자의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)가 실행되면, 동일한 [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 개체를 각자 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 하다가 두번 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)하게 되고, 결국 [예외가 발생](https://tango1202.github.io/legacy-cpp-exception/legacy-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw)합니다.

이렇게 [포인터 멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)의 소유권을 서로 가지고 있고, 서로 소멸시키는 현상을 **소유권 분쟁**이라 합니다. 

**소유권 분쟁**을 해결하는 방법은 

1. 소유권 이전을 하거나(*[auto_ptr](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-auto_ptr/), [unique_ptr](https://tango1202.github.io/mordern-cpp/mordern-cpp-unique_ptr/)*),
2. 깊은 복제를 하거나, 
3. 자원을 공유하거나(*[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/)*), 
4. 유일한 자원으로 대체해서 사용하는

방법이 있습니다. 

여기서는 깊은 복제 방법으로 구현해 보도록 하겠습니다.

깊은 복제를 하기 위해서는 [암시적 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 대신, 다음처럼 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 명시적으로 구현하여, [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 개체의 복제본을 만들면 됩니다.

```cpp
class T {
    int* m_Val;
public:
    // val : new 로 생성된 것을 전달하세요.
    explicit T(int* val) :
        m_Val(val) {}

    // (O) NULL 포인터가 아니라면 복제합니다.
    T(const T& other) :
        m_Val(other.m_Val != NULL ? new int(*other.m_Val) : NULL) {}

    // 힙 개체를 메모리에서 제거 합니다.
    ~T() {delete m_Val;} 
};
// (O) 힙 개체를 복제하여 소유권 분쟁이 없습니다.
{
    T t1(new int(10));
    T t2(t1); // 새로운 int형 개체를 만들고 10을 복제합니다.
} 
```

이제, `t1`, `t2`는 다른 메모리 영역을 가리키므로, 각자 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8) 해도 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/204850fc-ed5b-4eb0-a7b6-e939d722c918)

# 복사 생성자만 지원하는 스마트 포인터

[힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 개체의 복제본을 만들기 위해 클래스마다 일일이 명시적으로 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 개발하는 것 보다는, [암시적 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 그대로 사용할 수 있도록 스마트 포인터(*[shared_ptr](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-shared_ptr-weak_ptr/) 등*)를 만들어 사용하는게 코드도 간결하고 분석하기 좋습니다. 여기서는 `int`형을 지원하는 것만 구현해 보도록 하겠습니다.(*복사 대입 연산까지 지원하는 것은 [복사 대입 연산자까지 지원하는 스마트 포인터](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90%EA%B9%8C%EC%A7%80-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EC%8A%A4%EB%A7%88%ED%8A%B8-%ED%8F%AC%EC%9D%B8%ED%84%B0)를 참고하세요. 그리고, 모든 타입을 지원하는 일반화된 스마트 포인터의 구현 예는 [auto_ptr](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-auto_ptr/)을 참고하세요.*)

스마트 포인터는 다음 단계를 통해 포인터 복제를 대행하고, [유효 범위](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-scope/)를 벗어나 자동 소멸될 때(*[소멸자 호출 시점](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EC%86%8C%EB%A9%B8%EC%9E%90-%ED%98%B8%EC%B6%9C-%EC%8B%9C%EC%A0%90) 참고*) 포인터를 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)합니다. 

1. #1 : 스마트 포인터를 클래스 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)로 정의해 둡니다.
2. #2 : [암시적 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출되면, 내부적으로 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)들의 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다. 이때 스마트 포인터의 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출됩니다.
3. #3 : 스마트 포인터의 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)에서 포인터 복제를 합니다.
4. #4 : 개체의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/) 호출뒤 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)들이 소멸됩니다.(*[개체 소멸 순서](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/#%EA%B0%9C%EC%B2%B4-%EC%86%8C%EB%A9%B8-%EC%88%9C%EC%84%9C) 참고*)
5. #5 : [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/) 소멸시 스마트 포인터의 [소멸자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-destructors/)에서 포인터를 [delete](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-new-delete/#%EA%B0%9C%EC%B2%B4-%EC%83%9D%EC%84%B1%EC%86%8C%EB%A9%B8)합니다.

```cpp
// 복사 생성시 m_Ptr을 복제하고, 소멸시 delete 합니다.(복사 대입 연산은 지원하지 않습니다.)
class IntPtr {
private:
    int* m_Ptr; // new로 생성된 개체입니다.
public: 
    explicit IntPtr(int* ptr) :
        m_Ptr(ptr) {}

    // (O) NULL 포인터가 아니라면 복제합니다.    
    IntPtr(const IntPtr& other) : // #2
        m_Ptr(other.IsValid() ? new int(*other.m_Ptr) : NULL) {} // #3

    // 힙 개체를 메모리에서 제거 합니다.
    ~IntPtr() { // #4
        delete m_Ptr; // #5
    }

    // 포인터 연산자 호출시 m_Ptr에 접근할 수 있게 합니다.
    const int* operator ->() const {return m_Ptr;}
    int* operator ->() {return m_Ptr;}

    const int& operator *() const {return *m_Ptr;}
    int& operator *() {return *m_Ptr;}

    // 유효한지 검사합니다.
    bool IsValid() const {return m_Ptr != NULL ? true : false;}    
};

class T {
    // (O) IntPtr로 복사 생성시 포인터의 복제본을 만들고, 소멸시 IntPtr에서 delete 합니다.
    // (O) 암시적 복사 생성자에서 정상 동작하므로, 명시적으로 복사 생성자를 구현할 필요가 없습니다.
    IntPtr m_Val; // #1
public:
    // val : new 로 생성된 것을 전달하세요.
    explicit T(int* val) :
        m_Val(val) {}
    int GetVal() const {return *m_Val;}
};
// (O) 힙 개체를 복제하여 소유권 분쟁 없이 각자의 힙 개체를 delete 합니다.
{
    T t1(new int(10));
    T t2(t1); // 새로운 int형 개체를 만들고 10을 복제합니다.

    EXPECT_TRUE(t2.GetVal() == 10);
} 
// (X) 예외 발생. 2번 delete 합니다. 아직 복사 대입 연산은 지원하지 않습니다.
{
    T t1(new int(10));
    T t2(new int(20));
    t2 = t1; // 아직 복사 대입 연산은 지원하지 않습니다.
}
```

`T t2(t1);`은 `t2`의 [암시적 복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하는데요, `IntPtr`의 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)에 의해 `t1.m_Val.m_Ptr` 값이 `t2.m_Val.m_Ptr`에 복제됩니다. 따라서 `t1`, `t2`는 서로 다른 [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 개체를 참조하게 되고, `t1`, `t2` 소멸시 각자의 [힙](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-memory-segment/#%ED%9E%99) 개체를 소멸하게 됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1e9f46e6-c73d-438f-adb0-819445dde03c)

# 생성자에서 가상 함수 호출 금지

부모 클래스의 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)에서 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하면, 아직 자식 클래스들이 완전히 생성되지 않은 상태이기에 부모 클래스의 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)가 호출됩니다. 의도치 않은 동작이므로, [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)에서는 [가상 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하지 마세요.

```cpp
class Base {
protected:    
    int m_Val;
public:
    Base() : 
        m_Val(0) {
        // (X) 오동작. 가상 함수를 생성자에서 호출합니다.
        // Derived::SetVal()이 호출되길 기대하지만, 
        // Base::SetVal()이 호출됩니다.    
        SetVal(); 
    }
    virtual void SetVal() {
        m_Val = 1; // Base 에서는 1
    }
    int GetVal() const {return m_Val;}
};

class Derived : public Base {
public:
    Derived() :
        Base() {} // Base의 기본 생성자를 호출하면서 가상 함수 SetVal()이 호출됩니다.
    virtual void SetVal() {
        m_Val = 2; // Derived 에서는 2
    }
};

Derived d;

// (X) 오동작. Base 생성자에서 가상 함수인 SeVal()을 호출하면, 
// Derived::SetVal()이 호출되길 기대하나,
// 아직 Derived가 완전히 생성되지 않은 상태이기에,
// Base::SetVal()이 호출됨
EXPECT_TRUE(d.GetVal() == 1); 
```

# 기본 생성자, 복사 생성자 사용 제한

만약 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)나 [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 필요없다면, [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)를 사용할 수 없게 만드는게 좋습니다. 어짜피 사용하지 않을거라 내버려 뒀는데, 누군가가 유지보수 하면서 무심결에 사용하게 된다면, 오동작을 할 수 있거든요. ***의도하지 않았다면 동작하지 않게 해야 합니다.***

사용을 제한하는 방법은 다음과 같습니다.

* [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90) : 다른 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)(*[값 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)던, [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)던*)가 정의되면, [암시적 기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 정의되지 않으므로 사용이 제한됩니다. 그렇지 않은 경우 명시적으로 [기본 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 구현하고, `private` 또는 `protected`로 사용을 제한합니다.
* [복사 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) : `private`로 정의하면 다른 곳에서는 사용하지 못하고(*[Uncopyable](https://tango1202.github.io/cpp-coding-pattern/cpp-coding-pattern-uncopyable/) 참고*), `protected`로 정의하면 [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)받은 자식 개체에서만 사용할 수 있습니다.

```cpp
class T {
public:
    T(int, int) {} // (O) 값 생성자를 정의하면 암시적 기본 생성자를 사용할 수 없습니다.
private:
    T(const T& other) {} // (O) private여서 외부에서 복사 생성자 사용할 수 없습니다.
};

T t1; // (X) 컴파일 오류. 기본 생성자 없음
T t2(0, 0); // (O)
T t3(t1); // (X) 컴파일 오류. 복사 생성자를 사용할 수 없게 private로 하여 단단히 코딩 계약을 했습니다.
```

> *(C++11~) [default, delete](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#default%EC%99%80-delete)가 추가되어 [암시적으로 생성되는 멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-implicit-definition/)의 사용 여부를 좀더 명시적으로 정의할 수 있습니다.*

# 상속 전용 기반 클래스 - protected 생성자

[상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)해서만 사용할 수 있는 클래스는 [protected 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%83%81%EC%86%8D-%EC%A0%84%EC%9A%A9-%EA%B8%B0%EB%B0%98-%ED%81%B4%EB%9E%98%EC%8A%A4---protected-%EC%83%9D%EC%84%B1%EC%9E%90)로 만듭니다. 그러면 개체 정의(*인스턴스화*)에서는 사용할 수 없고, [상속](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/)해서만 사용할 수 있습니다.(*[상속 강제](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EA%B0%95%EC%A0%9C) 참고*)


```cpp
class Base {
protected: // 개체 정의(인스턴스화)에서는 사용할 수 없고, 상속해서만 사용할 수 있습니다.
    Base() {} 
public:
    virtual void f() {}
};
class Derived : Base {
    virtual void f() {}
};

Base b; // (X) 컴파일 오류
Derived d; // (O) 상속하면 인스턴스화 가능
```

> *(C++11~) [final](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#final)이 추가되어 강제적으로 [상속을 제한](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C)할 수 있습니다.*

# 생성자 접근 차단 - private 생성자

외부에서 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/) 접근을 못하게 하려면 [private 생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A0%91%EA%B7%BC-%EC%B0%A8%EB%8B%A8---private-%EC%83%9D%EC%84%B1%EC%9E%90)로 만듭니다. 이때, 생성을 위한 `Create()`계열 함수를 별도로 만들 수 있습니다. [상속을 제한](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-inheritance/#%EC%83%81%EC%86%8D-%EC%A0%9C%ED%95%9C)하거나, 다양한 생성 방식을 개체에서 통제하고 싶을때 사용합니다.

```cpp
class T {
private:
    T(int a, int b, int c) {} // 외부에서는 접근 불가
public:
    static T CreateFromA(int a) {return T(a, 0, 0);} // a값만 가지고 생성
    static T CreateFromB(int b) {return T(0, b, 0);} // b값만 가지고 생성
    static T CreateFromC(int c) {return T(0, 0, c);} // c값만 가지고 생성
};

// T t(10, 0, 0); // (X) 컴파일 오류
// T* p = new T(10, 0, 0); // (X) 컴파일 오류

class U : public T {};
// U u; // (X) 컴파일 오류. 상속해서 생성할 수 없음
// U* p = new u; // (X) 컴파일 오류  

T t(T::CreateFromA(10)); // (O) T를 복사 생성    
```

> *(C++11~) [생성자 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84)이 추가되어 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)의 [초기화 리스트](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8) 코드가 좀더 간결해 졌습니다. 상기 예는 [생성자 위임](https://tango1202.github.io/mordern-cpp/mordern-cpp-class/#%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9C%84%EC%9E%84)을 통해서도 구현할 수 있습니다.*

혹은 [생성자](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-constructors/)에서 모든 처리를 하기 힘든 개체를 생성할때도 유용합니다.(*시스템 종속성이 높다던지, 일단 생성후 환경 정돈을 해야 한다던지 등이요.*)

```cpp
class T {
private:
    T() {} // 외부에서 접근 불가
public:
    static T* Create() {
        T* result = new T; // 기본 생성자를 만들고,
        GlobalSetter.f(); // 생성후 사전에 해야할 전역 설정을 하고,
        T->Func(GlobalGetter.f()); // 전역 설정을 참조하여 Func()을 실행하고
        ... // 뭔가를 열심히 더하고...

        // 이제 T 개체 생성에 따른 주변 환경도 다 설정했으므로 리턴
        return result; 
    }
};
```

