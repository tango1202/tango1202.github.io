---
layout: single
title: "#17. [모던 C++] (C++11~) constexpr, constexpr 함수, constexpr 생성자, (C++17~) if constexpr, (C++20~) consteval 함수, constinit 변수"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#15] 가능하면 항상 constexpr을 사용하라.(컴파일 타임, 런타임 모두 사용할 수 있다)

> * (C++11~) [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)이 추가되어 컴파일 타임 프로그래밍이 강화됐습니다.
> * (C++14~) [constexpr 함수 제약이 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)되어 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), 2개 이상의 리턴문, `if()`, `for()`, `while()` 등을 사용할 수 있습니다.
> * (C++17~) [if constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c17-if-constexpr)이 추가되어 조건에 맞는 부분만 컴파일하고, 그렇지 않은 부분은 컴파일에서 제외할 수 있습니다.
> * (C++20~) [consteval 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-consteval-%ED%95%A8%EC%88%98)가 추가되어 컴파일 타임 함수로만 동작할 수 있습니다.
> * (C++20~) [constinit](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constinit-%EB%B3%80%EC%88%98)가 추가되어 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)를 컴파일 타임에 초기화할 수 있습니다.
> * (C++20~) [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)가 한차례 더 보강되어 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98), [dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98), [typeid()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90), 초기화되지 않은 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/), [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 활성 전환, `asm`등을 사용할 수 있습니다.

# 개요

[템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/) 에서 언급한 것처럼 컴파일 타임에 여러가지 프로그래밍이 가능합니다. 하지만, [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)으로 우회하며 작성하다보니 상당히 난해했는데요, 

C++11 부터는 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr) 이용해 **컴파일 타임 상수 표현식**을 지정할 수 있어 컴파일 타임 프로그래밍 환경이 좀더 쉬워졌습니다.

# constexpr

컴파일 타임 상수로 지정합니다. 

컴파일 타임 상수는 읽기 전용 메모리인 [코드 세그먼트](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%EC%BD%94%EB%93%9C-%EC%84%B8%EA%B7%B8%EB%A8%BC%ED%8A%B8)에 할당되므로 [예외에 안전](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-safe/)합니다. 할 수 있다면 최대한 컴파일 타임 상수로 만드는게 좋습니다.

컴파일 타임 상수는 다음과 같이 [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/)의 값지정이나 [비타입 템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 전달을 통해 확인 할 수 있습니다.

`const int` 를 사용하면 컴파일 타임 상수로 취급되는되요,

```cpp
template<int val>
class T {
public:
    int operator ()() {return val;} 
};

const int size{20}; // 상수 입니다.

enum class MyEnum_11 {Val = size}; // (O) size는 컴파일 타임 상수 입니다.
T<size> t; // (O) size는 컴파일 타임 상수 입니다.
```

하지만, 변수로부터 `const int`를 초기화 하면, 런타임 상수이지, 컴파일 타임 상수가 아니어서 컴파일 오류가 납니다.

```cpp
int a{20};
const int size{a}; // 변수로부터 const int를 초기화 해서 런타임 상수 입니다.

enum class MyEnum_11 {Val = size}; // (X) 컴파일 오류. size는 런타임 상수 입니다.
T<size> t; // (X) 컴파일 오류. size는 런타임 상수 입니다.
```

[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)은 좀더 명시적으로 컴파일 타임 상수임을 알려 줍니다.

```cpp
constexpr int size_11{20}; // 컴파일 타임 상수 입니다.

enum class MyEnum_11 {Val = size_11}; // (O)
T<size_11> t; // (O) 
```

[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)은  `const int`와 달리 변수를 대입하면 컴파일 오류가 발생합니다.

```cpp
int a{20};
constexpr int size_11{a}; // (X) 컴파일 오류. 상수를 대입해야 합니다.
```

# constexpr 함수

기존에는 컴파일 타임 함수가 없었고, 함수처럼 동작하도록 다음처럼 [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)을 활용했는데요(*[템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/) 참고*),

```cpp
// Factorial n -1 을 재귀 호출합니다.
template<unsigned int n>
struct Factorial { 
    enum {
        Val = n * Factorial<n-1>::Val
    };  
};
// 0일때 특수화 버전. 더이상 재귀 호출을 안합니다.
template<>
struct Factorial<0> { 
    enum {
        Val = 1
    }; 
};
// 컴파일 타임에 계산된 120이 Val에 대입됩니다.
enum class MyEnum {Val = Factorial<5>::Val};
EXPECT_TRUE(static_cast<int>(MyEnum::Val) == 1 * 2 * 3 * 4 * 5);
```

C++11 부터는 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)을 이용하여 암시적으로 [인라인 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-inline/)인 컴파일 타임 함수를 만들 수 있습니다.

1. 컴파일 타임 상수를 전달하면 컴파일 타임 함수로 동작하고,
2. 일반 변수를 전달하면, 일반 함수들처럼 런타임 함수로 동작합니다.

    > *(C++20~) [consteval 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-consteval-%ED%95%A8%EC%88%98)가 추가되어 컴파일 타임 함수로만 동작할 수 있습니다.*

```cpp
constexpr int Factorial_11(int val) {
    // 마지막으로 1에 도달하면 재귀호출 안함
    return val == 1 ? val : val * Factorial_11(val - 1);
}

// 컴파일 타임에 계산된 120이 Val에 대입됩니다.
enum class MyEnum_11 {Val = Factorial_11(5)};
EXPECT_TRUE(static_cast<int>(MyEnum_11::Val) == 1 * 2 * 3 * 4 * 5);       

// 변수를 전달하면, 일반 함수처럼 동작합니다.
int val{5};
int result{Factorial_11(5)};
```

런타임에 했던 작업을 최대한 컴파일 타임으로 전환하여 런타임 성능을 향상시킬 수 있는 획기적인 기능이지만 함수 구현에 제약이 좀 많습니다.

오로지 1개의 명령문으로만 작성해야 합니다.(*제약 조건은 점진적으로 완화되고 있습니다. [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)를 참고하세요.*)

1. 1개의 리턴문을 사용합니다.
2. 조건식 대신 [조건 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A1%B0%EA%B1%B4-%EC%97%B0%EC%82%B0%EC%9E%90)(`a ? b : c`)를 사용합니다. 
3. 반복문은 재귀 호출을 이용하여 구현합니다.

> *(C++14~) [constexpr 함수 제약이 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)되어 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), 2개 이상의 리턴문, `if()`, `for()`, `while()` 등을 사용할 수 있습니다.*<br/>
> *(C++20~) [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)가 한차례 더 보강되어 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98), [dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98), [typeid()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90), 초기화되지 않은 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/), [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 활성 전환, `asm`등을 사용할 수 있습니다.*


# constexpr 생성자 

[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)은 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)만 사용할 수 있습니다.

그러다 보니, 사용자 정의 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/), 사용자 정의 [소멸자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-destructors/)가 없으며 모든 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)가 `public`인 [집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)인 구조체/클래스인 경우에만 사용할 수 있는데요, 다행히, [constexpr 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)를 이용하여 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)인 구조체/클래스를 직접 만들어 사용할 수 있습니다.

다음 `Area_11` 클래스는 `private` [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)와 [생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/)를 갖고 있지만, [constexpr 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)를 사용하여 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)으로 동작합니다.

1. `Area_11(int x, int y)`에 컴파일 상수를 전달하면, `Area_11`도 컴파일 상수로 동작합니다.

2. `Area_11`이 컴파일 타임 상수로 생성되었다면, `GetVal_11()`은 컴파일 타임 상수끼리의 연산이므로 컴파일 타임 함수가 될 수 있습니다.

```cpp
class Area_11 {
private:
    int m_X;
    int m_Y;
public:
    constexpr Area_11(int x, int y) : // 컴파일 타임 상수로 사용 가능
        m_X(x),
        m_Y(y) {} 
    constexpr int GetVal_11() const {return m_X * m_Y;}
};

constexpr Area_11 area(2, 5); // 컴파일 타임 상수로 정의

// 컴파일 타임에 계산된 면적이 Val에 대입됩니다.
enum class MyEnum_11 {Val = area.GetVal_11()}; // constexpr 함수 호출
EXPECT_TRUE(static_cast<int>(MyEnum_11::Val) == 2 * 5);     
```

만약 다음의 `x`와 같이 변수를 전달한다면 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)을 사용할 수 없습니다. 런타임 함수로 사용해야 합니다.

```cpp
int x = 10;
// constexpr Area_11 area{x, 5}; // (X) 컴파일 오류. x는 컴파일 타임 상수가 아닙니다.
Area_11 area{x, 5}; // 런타임 함수로 동작합니다.
```

`GetVal_11()`와 같은 `Setter` 계열 함수들은 다음 제약 조건 때문에 만들기 어렵습니다.(*제약 조건은 점진적으로 완화되고 있습니다. [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)를 참고하세요.*)

1. [constexpr 함수](
https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)는 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)만 리턴합니다. 그런데 `void`가 리터럴이 아닙니다.(*C++14 부터는 `void`도 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)이 되어 가능합니다.*)

2. [constexpr 함수](
https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)는 암묵적으로 [상수 멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EC%83%81%EC%88%98-%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)이고 `operator =`등이 아예 허용되지 않습니다. [mutable](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5-%EC%A7%80%EC%A0%95%EC%9E%90mutable)도 못씁니다. 

다행히 C++14 부터는 상기 [제약이 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)되어 다음처럼 `Setter`를 만들 수 있습니다.

```cpp
class Area_11 {
private:
    int m_X;
    int m_Y;
public:
    constexpr Area_11(int x, int y) : // 컴파일 타임 상수로 사용 가능
        m_X{x},
        m_Y{y} {} 
    constexpr int GetVal_11() const {return m_X * m_Y;}
             
    // C++14 부터는 void를 리턴할 수 있고, constexpr개체에서도 멤버 변수의 값을 수정할 수 있습니다. 
    constexpr void SetX_14(int val) {m_X = val;}
    constexpr void SetY_14(int val) {m_Y = val;}           
}; 
```

# (C++14~) constexpr 함수 제약 완화

[constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)는 기본적으로 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)만 리턴할 수 있고, 그외 스펙들은 조금씩 개선되고 있습니다. 

C++11 에서는 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)나 [제어문](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-statements/)도 사용할 수 없어서 상당히 제한적이었으나, 점점 개선되고 있습니다. 자세한 내용은 [cppreference.com](https://en.cppreference.com/w/cpp/language/constexpr)을 참고하시기 바랍니다.

|항목|C++11|C++14|C++17|C++20|
|--|--|--|--|--|
|[리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85) 외의 리턴|X|X|X|X|
|[조건 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#%EC%A1%B0%EA%B1%B4-%EC%97%B0%EC%82%B0%EC%9E%90)|O|O|O|O|
|[static_assert()](https://tango1202.github.io/mordern-cpp/mordern-cpp-static-assert/)|O|O|O|O|
|[typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD), [using](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#using%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)|O|O|O|O|
|[constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98) 호출|O|O|O|O|
|초기화된 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 정의|X|O|O|O|
|초기화되지 않은 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 정의|X|X|X|O|
|1개의 리턴문|O|O|O|O|
|2개 이상의 리턴문|X|O|O|O|
|`if`, `for`, `while`|X|O|O|O|
|`goto`, `switch`|X|X|X|X|
|[void 사용](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)|X|O|O|O|
|[const 자유도](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)|X|O|O|O|
|[try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/)|X|X|X|O|
|`throw`|X|X|X|X|
|[가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)|X|X|X|O|
|[dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)|X|X|X|O|
|[typeid()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)|X|X|X|O|
|[공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)의 활성 전환|X|X|X|O|
|`asm`|X|X|X|O|

다음은 C++14 기준에 맞춰서 좀더 일반적인 형태로 `Factorial_14()`를 구현한 예입니다. [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), 2개 이상의 리턴문, `if()`, `for()`등을 사용할 수 있어서 코딩 자유도가 높아졌습니다.

```cpp
constexpr int Factorial_14(int val) {
    int result{1}; // 초기화된 지역 변수 정의

    if (val < 1) {
        return 1; // 2개 이상의 리턴문
    }

    for (int i{val}; 0 < i; --i) { // 제어문
        result *= i;
    }

    return result;
} 

// 컴파일 타임에 계산된 120이 Val에 대입됩니다.
enum class MyEnum_11 {Val = Factorial_14(5)};
EXPECT_TRUE(static_cast<int>(MyEnum_11::Val) == 1 * 2 * 3 * 4 * 5);       
```

> *(C++20~) [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)가 한차례 더 보강되어 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98), [dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98), [typeid()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90), 초기화되지 않은 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/), [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 활성 전환, `asm`등을 사용할 수 있습니다.*

# (C++17~) if constexpr 

조건식에 따라 컴파일하거나 컴파일하지 않습니다.

[템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/) 에서 소개된 다음 코드는 `if()`을 통해 `new T(*ptr);`와 `ptr->Clone();`을 호출하기 때문에, 이 두가지가 모두 가능한 것만 컴파일할 수 있었습니다.

```cpp
class ICloneable {
private:
    ICloneable(const ICloneable& other) {}
    ICloneable& operator =(const ICloneable& other) {return *this;}
protected:
    ICloneable() {}  
    ~ICloneable() {} 
public:
    virtual ICloneable* Clone() const = 0; 
};

// D가 B를 상속하였는지 검사하는 템플릿
template<typename D, typename B> 
class IsDerivedFrom {
    class No {};
    class Yes {No no[2];};

    static Yes Test(B*);
    static No Test(...);
public:
    enum {Val = sizeof(Test(static_cast<D*>(0))) == sizeof(Yes)};
};

template<typename T>
class CloneTraits {
public:

    static T* Clone(const T* ptr) {
    	if (ptr == NULL) {
    		return  NULL;
    	}

        if (!IsDerivedFrom<T, ICloneable>::Val) {
            return new T(*ptr);
        }
        else {
            return ptr->Clone(); // (X) 컴파일 오류. int에 Clone() 함수가 없습니다.
        }
    } 
};

int val;
int* ptr{CloneTraits<int>::Clone(&val)}; // (X) 컴파일 오류. int에 Clone() 함수가 없습니다.
delete ptr;
```

따라서, `CloneTraits`를 구현할때 컴파일 타임 프로그래밍을 위해 `if()`문을 사용하지 말고, `CloneTag`를 이용하여 오버로딩하라 소개했는데요,(*[템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)의 [CloneTraits 구현](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/#clonetraits-%EA%B5%AC%ED%98%84)에서 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)을 이용하는 방법 참고*)

C++17 부터는 [if constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c17-if-constexpr)이 추가되어 조건에 맞는 부분만 컴파일하고, 그렇지 않은 부분은 컴파일에서 제외할 수 있습니다. 

즉, 다음과 같이 작성하면,

```cpp
template<typename T>
class CloneTraits {
public:
    static T* Clone_17(const T* ptr) {
        if (ptr == NULL) {
            return  NULL;
        }

        // 조건에 맞는 부분만 컴파일 합니다.
        if constexpr (!IsDerivedFrom<T, ICloneable>::Val) {
            return new T(*ptr);
        }
        else {
            return ptr->Clone(); 
        }
    } 
};
int val;
int* ptr{CloneTraits<int>::Clone_17(&val)}; // (O)
delete ptr; 
```

다음과 동등하기 때문에 컴파일되고 잘 작동합니다. 

```cpp
static T* Clone_17(const T* ptr) {
    if (ptr == NULL) {
        return  NULL;
    }

    return new T(*ptr); // return ptr->Clone();은 조건에 맞지 않으므로 컴파일 타임에 제외되었습니다.
} 
int val;
int* ptr{CloneTraits<int>::Clone_17(&val)}; // (O)
delete ptr; 
```

# (C++20~) consteval 함수

[constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)는 

1. 컴파일 타임 상수를 전달하면 컴파일 타임 함수로 동작하고,
2. 일반 변수를 전달하면, 일반 함수들처럼 런타임 함수로 동작했는데요(*[constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98) 참고*),

C++20 부터는 [consteval 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-consteval-%ED%95%A8%EC%88%98)가 추가되어 컴파일 타임 함수로만 동작할 수 있습니다. 이렇게 컴파일 타임에만 동작하는 함수를 즉시 함수(*immediate function*)라고 합니다.

```cpp
constexpr int Add_14(int a, int b) {return a + b;}
consteval int Add_20(int a, int b) {return a + b;}

enum class MyEnum_11 {Val = Add_14(1, 2)}; // 컴파일 타임 함수로 사용
EXPECT_TRUE(static_cast<int>(MyEnum_11::Val) == 3); 

enum class MyEnum_11 {Val = Add_20(1, 2)}; // 컴파일 타임 함수로 사용
EXPECT_TRUE(static_cast<int>(MyEnum_11::Val) == 3); 

int a{10};
int b{20};
EXPECT_TRUE(Add_14(a, b) == 30); // 런타임 함수로 사용
EXPECT_TRUE(Add_20(a, b) == 30); // (X) 컴파일 오류. 런타임 함수로 사용할 수 없습니다.
```

[consteval 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-consteval-%ED%95%A8%EC%88%98)도 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)와 같이 제약이 있으며, [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)를 참고하시기 바랍니다.

# (C++20~) constinit 변수

[전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)는 프로그램이 시작될 때 생성되고, 프로그램이 종료될 때 해제됩니다. 하지만 생성되는 시점이 명확하지 않아 링크 순서에 따라 다음 예의 `g_B`가 `0`이 되거나 `10`이 됩니다. 따라서, [함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)를 사용하라고 가이드 했었죠.(*[정적 변수의 초기화 순서](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%B3%80%EC%88%98%EC%9D%98-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%88%9C%EC%84%9C) 참고*)

```cpp
// Test_A.cpp에서
int f() {return 10;}
int g_A = f(); // 전역 변수. 런타임에 f() 함수를 이용해서 초기화 합니다.
```

```cpp
// Test_B.cpp에서
#include <iostream>

extern int g_A;
int g_B = g_A; // (△) 비권장. 컴파일 단계에선 일단 0으로 초기화 하고, 나중에 링크 단계에서 g_A의 값으로 초기화 합니다.
               // g_A가 초기화 되었다는 보장이 없기에 링크 순서에 따라 0 또는 10이 됩니다.

int main() {
    std::cout << "g_A : " << g_A << std::endl;
    std::cout << "g_B : " << g_B << std::endl; // (△) 비권장. 0이 나올 수도 있고, 10이 나올 수도 있습니다.

    return 0;
}
```

C++20 부터는 [constinit](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constinit-%EB%B3%80%EC%88%98)가 추가되어 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)를 컴파일 타임에 초기화할 수 있습니다. 따라서, [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)의 생성 시점이 명확하지 않은 문제가 해결 됩니다.(*하지만, 이 방법 보다는 여전히 [함수내 정적 지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%ED%95%A8%EC%88%98%EB%82%B4-%EC%A0%95%EC%A0%81-%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)를 사용하는게 좋아 보입니다.*)

상기 코드에 [constinit](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constinit-%EB%B3%80%EC%88%98)을 사용하면, 링크 타임이 아니라 컴파일 타임에 초기화 되므로, 링크 순서에 따라 `g_B`가 `0`이 되거나 `10`이 되는 문제를 수정할 수 있습니다.

```cpp
// Test_A.cpp에서
constexpr int f_11() {return 10;} // 컴파일 타임 상수 입니다.
constinit int g_A_20 = f_11(); // 컴파일 타임에 초기화 됩니다.
```

```cpp
// Test_B.cpp에서
#include <iostream>

extern constinit int g_A_20;
int g_B_20 = g_A_20; // 컴파일 타임에 초기화 됩니다.

int main() {
    std::cout << "g_A_20 : " << g_A_20 << std::endl;
    std::cout << "g_B_20 : " << g_B_20 << std::endl; // 항상 10이 나옵니다.

    return 0;
}
```

컴파일 타임에 초기화된다는 측면에서 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)과 유사하지만, 다음과 같은 차이가 있습니다.

|항목|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr)|[constinit](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constinit-%EB%B3%80%EC%88%98)|
|--|--|--|
|의미|컴파일 타임 상수 입니다.|컴파일 타임에 초기화되어야 합니다.|
|[상수성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)|[const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)입니다.|[constinit](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constinit-%EB%B3%80%EC%88%98)의 이름 때문에 오해하기 쉬운데, [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)가 아닙니다. 상수로 만드려면, `constinit const int`와 같이 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 별도로 붙여야 합니다.|
|[지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 적용|지원합니다.|지원하지 않습니다.<br/>[전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)에만 사용 가능합니다.|

다음은 [constinit](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constinit-%EB%B3%80%EC%88%98)를 [전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 전역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98), [정적 멤버 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)에 사용한 예입니다.

```cpp
constexpr int f_11() {return 10;} // 컴파일 타임 함수 입니다.

// constinit int 로 받을 수도 있지만, s_Val_20과 s_m_Val_20에 대입하기 위해 const를 붙였습니다.
constinit const int g_Val_20 = f_11(); // constinit여서 컴파일 타임에 초기화 되어야 합니다.
    
// g_Val_20은 컴파일 타임 const 상수 입니다.
constinit static int s_Val_20 = g_Val_20; // constinit여서 컴파일 타임에 초기화 되어야 합니다.

class T_20 {
public:
    // C++17 부터 인라인 변수를 이용하여 정적 멤버 변수를 멤버 선언부에서 초기화할 수 있습니다.
    constinit static inline int s_m_Val_20 = g_Val_20; // constinit여서 컴파일 타임에 초기화 되어야 합니다.
};
```

# (C++20~) constexpr 함수 제약 완화

C++14 에서 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)의 제약이 완화되었으나(*[(C++14~) constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c14-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94) 참고*), 

C++20 에서는 [constexpr 함수 제약 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#c20-constexpr-%ED%95%A8%EC%88%98-%EC%A0%9C%EC%95%BD-%EC%99%84%ED%99%94)가 한차례 더 보강되어 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98), [dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98), [typeid()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90), 초기화되지 않은 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98), [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/), 공용체 멤버 변수 활성 전환, `asm`등을 사용할 수 있습니다.

다음예에서 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 오버라이딩한 `Func` 함수를 컴파일 타임 상수로 사용할 수 있고, 이를 `Base*`를 통해 [가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)를 호출하면, 런타임에 동작하는 걸 확인 할 수 있습니다.

```cpp
class Base {
public:
    virtual int Func() const {return 0;}
};
class Derived_20 : public Base {
public:
    constexpr virtual int Func() const override {return 1;} // C++17 이하에서는 컴파일 오류가 발생했습니다.
};

constexpr Derived_20 a_20;
enum class MyEnum_11 {Val = a_20.Func()}; // 컴파일 타임 상수

const Base* ptr = &a_20;
EXPECT_TRUE(ptr->Func() == 1); // 부모 개체의 포인터로 런타임에 가상 함수를 호출할 수 있습니다.
// enum class MyEnum_11 {Val = ptr->Func()}; // (X) 컴파일 오류. 컴파일 타임 상수가 아닙니다.
```

또한 [dynamic_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)와 [typeid()](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90)를 사용할 수 있습니다.

```cpp
class Base {
public:
    virtual int Func() const {return 0;}
};
class Derived_20 : public Base {
public:
    constexpr virtual int Func() const override {return 1;} 
};
    
constexpr int Func_20() {

    Derived_20 d;

    Base* base = &d;
    Derived_20* derived_20 = dynamic_cast<Derived_20*>(base); // dynamic_cast를 사용할 수 있습니다.
    typeid(base); // typeid를 사용할 수 있습니다.
    
    return 1;
}
```

그외에 초기화되지 않은 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 정의, [try-catch()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/), `asm`(*인라인 어셈블리*)이 허용됩니다.

```cpp
constexpr void Func_20() {
    int a; // 초기화되지 않은 지역 변수

    try {} // try-catch
    catch(...) {}
}
```

또한 [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) 멤버 변수의 활성 전환이 허용됩니다.

기존에는 [constexpr 함수](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%ED%95%A8%EC%88%98)에서 [공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/#%EA%B3%B5%EC%9A%A9%EC%B2%B4) 멤버 변수의 활성을 전환하면 컴파일 오류가 있었는데요, C++20 부터는 허용합니다.

```cpp
union MyUnion {
    int i;
    float f;
};

constexpr int Func_20() {
 #if 202002L <= __cplusplus // C++20~     
    MyUnion myUnion{};

    myUnion.i = 3;
 
    // (X) ~C++20 컴파일 오류. change of the active member of a union from 'MyUnion::i' to 'MyUnion::f'
    // (O) C++2O~       
    myUnion.f = 1.2f; // 멤버 변수 활성 전환을 허용합니다.
#endif         
    return 1;
}

enum class MyEnum_11 {Val = Func_20()}; 
```