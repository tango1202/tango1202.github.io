---
layout: single
title: "#19. [모던 C++] (C++11~) extern 템플릿, (C++14~) 변수 템플릿, (C++17~) 템플릿 인수 추론, 비타입 템플릿 인자의 auto 허용, (C++20~) 축약된 함수 템플릿, 비타입 템플릿 인자 규칙 완화, 클래스 템플릿 인수 추론시 initializer_list 개선"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) `[extern으로 템플릿 선언](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#extern-%ED%85%9C%ED%94%8C%EB%A6%BF)을 할 수 있으며, 템플릿 인스턴스 중복 생성을 없앨 수 있습니다. 
> * (C++11~) [템플릿 오른쪽 꺽쇠 괄호](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%98%A4%EB%A5%B8%EC%AA%BD-%EA%BA%BD%EC%87%A0-%EA%B4%84%ED%98%B8) 파싱을 개선하여 [템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)시 `>`가 중첩되어 `>>`와 같이 되더라도 공백을 추가할 필요가 없습니다.
> * (C++14~) [변수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 변수도 템플릿으로 만들 수 있습니다.
> * (C++17~) [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 추가되어 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)처럼 타입을 생략할 수 있습니다.
> * (C++17~) [비타입 템플릿 인자에서 auto를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%EB%B9%84%ED%83%80%EC%9E%85-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90%EC%9D%98-auto-%ED%97%88%EC%9A%A9)합니다.
> * (C++20~) [축약된 함수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%EC%B6%95%EC%95%BD%EB%90%9C-%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용할 수 있습니다. 사실상 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)의 간략한 표현입니다.
> * (C++20~) [비타입 템플릿 인자 규칙이 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%EB%B9%84%ED%83%80%EC%9E%85-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90-%EA%B7%9C%EC%B9%99-%EC%99%84%ED%99%94)되어 실수 타입과 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)을 사용할 수 있습니다.
> * (C++20~) [클래스 템플릿 인수 추론시 initializer_list인 경우가 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0%EC%8B%9C-initializer_list-%EA%B0%9C%EC%84%A0)되어 `std::vector v_20{1, 2, 3};`처럼 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 명시하지 않아도 됩니다.

# extern 템플릿

기존에는 템플릿을 헤더 파일에 정의해 두고, 여러 cpp 파일에서 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 했었는데요, 이러면 템플릿 정의가 중복 정의되어 코드 크기가 커집니다.(*[템플릿 인스턴스 중복 생성](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EC%A4%91%EB%B3%B5-%EC%83%9D%EC%84%B1) 참고*)

```cpp
// ----
// Test_MordernCpp_ExternTemplate.h 에서
// ----
namespace ExternTemplate {
    template<typename T>
    T Add(T a, T b) {
        return a + b;
    }
}

// ----
// test1.cpp 에서
// ----
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate1) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(1, 2) == 3); // Add<int>()가 정의 되어 포함됩니다.
}

// ----
// test2. cpp 에서
// ----
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate2) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(10, 20) == 30); // Add<int>가 재정의 되어 포함됩니다.
}
```

C++11 부터는 [extern 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#extern-%ED%85%9C%ED%94%8C%EB%A6%BF)을 추가하여 템플릿 선언만 할 수 있으며, 불필요한 코드 크기를 최소화 할 수 있습니다. 

```cpp
// ----
// Test_MordernCpp_ExternTemplate.h 에서
// ----
namespace ExternTemplate {
    template<typename T>
    T Add(T a, T b) {
        return a + b;
    }
}

// ----
// test1.cpp 에서
// ----
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate1) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(1, 2) == 3); // Add<int>()가 정의 되어 포함됩니다.
}

// ----
// test2. cpp 에서
// ----
#include "Test_MordernCpp_ExternTemplate.h"

// 이전에 정의된 템플릿을 사용합니다.
extern template int ExternTemplate::Add<int>(int, int); // C++11

TEST(TestMordern, ExternTemplate2) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(10, 20) == 30); 
}
```

# 템플릿 오른쪽 꺽쇠 괄호

[템플릿 인스턴스화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%A0%95%EC%9D%98%EB%B6%80%EC%99%80-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%ED%99%94)시 템플릿 개체로 인스턴스화 하면 `>`이 중첩됩니다.

기존에는 이게 비트 Right Shift 연산자 `>>` 로 파싱되어 컴파일 되지 않았습니다. 따라서, `vector<A<int> >`와 같이 억지로 띄어쓰기를 했는데요(*[템플릿 파싱 오류](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98) 참고*), 

C++11 부터는 파싱을 개선하여 `vector<A<int>>`와 같이 `>>`을 사용해도 됩니다.

```cpp
template<typename T>
class A {};

std::vector<A<int> > a; // C++03. 공백을 추가해야 했습니다.
std::vector<A<int>> b_11; // C++11
```

# (C++14~) 변수 템플릿

기존의 템플릿은 클래스와 함수만 지원했는데요(*[템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/) 참고*),

C++14 부터는 [변수도 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)으로 만들 수 있습니다.

다음 예는 동일한 값을 지정한 `pi`를 타입에 따라 다른 정밀도로 사용하는 예입니다.

```cpp
template<class T>
// constexpr T pi_14{3.1415926535897932385L}; // 중괄호 초기화는 암시적 형변환이 안되서 = 로 초기화 합니다.
constexpr T pi_14 = 3.1415926535897932385L; 

// 동일한 값을 타입에 따라 서로 다른 정밀도로 사용할 수 있습니다.
std::cout << "pi_14<double> : " << std::setprecision(10) << pi_14<double> << std::endl;
std::cout << "pi_14<float> : " << std::setprecision(10) <<pi_14<float> << std::endl;
std::cout << "pi_14<int> : " << std::setprecision(10) <<pi_14<int> << std::endl;
```

```cpp
pi_14<double> : 3.141592654
pi_14<float> : 3.141592741 // double 보다 정밀도가 낮습니다.
pi_14<int> : 3
```

또한, 템플릿 특수화를 이용하여 템플릿 메타 프로그래밍에 활용할 수 있습니다. 

다음은 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)에서 소개한 `Factorial` 을 [변수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c14-%EB%B3%80%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)으로 구현한 예입니다.

```cpp
// 1을 뺀 값을 재귀 호출합니다.
template<int val> 
constexpr int factorial_14 = val * factorial_14<val - 1>; 

// 1일때 특수화 버전. 더이상 재귀 호출을 안합니다.
template<>
constexpr int factorial_14<1> = 1;

EXPECT_TRUE(factorial_14<5> == 5 * 4 * 3 * 2 * 1);
```

# (C++17~) 클래스 템플릿 인수 추론

기존에는 함수 템플릿만 인수를 추론하고 클래스 템플릿은 인수를 추론하지 않았는데요(*[함수 템플릿 인수 추론](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-argument-deduction/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 참고*),

```cpp
template<typename T>
T Func(T a, T b) {return a + b;}

EXPECT_TRUE(Func(1, 2) == 3); // 인수 1, 2 로부터 추론해서 사용합니다.

template<typename T>
class A {
public:
    explicit A(T val) {}
    T Func(T a, T b) {return a + b;}
};

A<int> a{10}; // ~C++17 이전에는 타입을 명시해야 합니다.
EXPECT_TRUE(a.Func(1, 2) == 3);
```

C++17 부터는 클래스 [템플릿도 생성자에 전달된 인수를 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)하므로 다음과 같이 사용할 수 있습니다.

```cpp
A a_17{10}; // C++17 이후부터는 인수로부터 추론합니다.
EXPECT_TRUE(a_17.Func(1, 2) == 3); 
```

> *(C++20~) [클래스 템플릿 인수 추론시 initializer_list인 경우가 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0%EC%8B%9C-initializer_list-%EA%B0%9C%EC%84%A0)되어 `std::vector v_20{1, 2, 3};`처럼 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 명시하지 않아도 됩니다.*

# (C++17~) 비타입 템플릿 인자의 auto 허용

C++17 부터는 [비타입 템플릿 인자에서 auto를 허용](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%EB%B9%84%ED%83%80%EC%9E%85-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90%EC%9D%98-auto-%ED%97%88%EC%9A%A9)합니다.

기존에는 임의 타입의 개체를 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)로 사용할때, 다음과 같이 타입(`T`)과 개체(`val`)를 같이 전달받았는데요,(*[템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 참고*)

```cpp
template<typename T, T val> // 타입과 개체를 전달받습니다.
class A {
public:
    T GetVal() {return val;} 
};

A<int, 10> a{};
EXPECT_TRUE(a.GetVal() == 10);
```

C++17 부터는 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 이용하여 임의 타입 개체를 전달받을 수 있습니다.

```cpp
template<auto val> // 비타입인 경우 auto를 사용할 수 있습니다.
class A_17 {
public:
    auto GetVal() const {return val;}
};  

A_17<10> a_17{};
EXPECT_TRUE(a_17.GetVal() == 10);
```

> *(C++20~) [비타입 템플릿 인자 규칙이 완화](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%EB%B9%84%ED%83%80%EC%9E%85-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90-%EA%B7%9C%EC%B9%99-%EC%99%84%ED%99%94)되어 실수 타입과 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)을 사용할 수 있습니다.*

# (C++20~) 축약된 함수 템플릿

기존에는 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용할 수 없었고, C++14 부터 [일반화된 람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#c14-%EC%9D%BC%EB%B0%98%ED%99%94%EB%90%9C-%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)에서만 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용할 수 있었습니다.(*[함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter), [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto) 참고*)

C++20 부터는 [축약된 함수 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%EC%B6%95%EC%95%BD%EB%90%9C-%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)이 추가되어 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)를 사용할 수 있습니다. 사실상 [함수 템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%95%A8%EC%88%98-%ED%85%9C%ED%94%8C%EB%A6%BF)의 간략한 표현입니다.

```cpp
// template<typename T> 
// void f(T param);
void f_20(auto param) {} 
f_20(10);

// template<typename T, typename U>
// void g(T param1, U param2);
void g_20(auto param1, auto param2) {} // param1과 param2는 동일한 타입이라는 보장이 없습니다.
g_20(10, 3.14);

// template<typename... Params>
// void h_11(Params... params);
void h_20(auto... params) {} // 가변 템플릿도 지원합니다.
h_20(10, 3.14, "Hello");

// template<typename T, typename U>
// void i(T param1, U param2) {}
template<typename T>
void i_20(T param1, auto param2) {} // 템플릿 내에서 혼합해서 사용할 수 있습니다.
i_20(10, 3.14);
```

[auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto)나 [decltype(auto)](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#c14-decltypeauto)와 [가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 함께 사용할 수도 있습니다.

```cpp
int Sum_20(auto count, ...) { // 가변 인자를 사용하는 함수입니다.

    int result = 0;
    std::va_list paramList; // 가변 인자
    va_start(paramList, count); // 가변 인자 처리 시작
    for (int i = 0; i < count; ++i) {
        result += va_arg(paramList, int); // 가변 인자 추출
    }
    va_end(paramList); // 가변 인자 처리 끝
    return result;       
}

EXPECT_TRUE(Sum_20(3, 1, 2, 3) == 1 + 2 + 3);
```

# (C++20~) 비타입 템플릿 인자 규칙 완화

기존에는 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)에 비타입(타입이 아닌 개체)을 사용할 경우, 정수 타입, [열거형](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-enum/) 상수의 열거자, 전역 또는 정적 개체의 포인터/[참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 사용할 수 있었는데요(*[템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 참고*),

```cpp
template<int val>
class A {};
A<10> a; // 정수 타입

enum MyEnum{Val};
template<enum MyEnum myEnum>
class B {};
B<Val> b; // 열거형의 열거자

class MyClass {};
template<MyClass* ptr>
class C {};

template<MyClass& ref>
class D {};  

MyClass g_MyClass;
C<&g_MyClass> c; // 전역, 정적 개체의 포인터
D<g_MyClass> d; // 전역, 정적 개체의 참조자
```

C++20 부터는 [비타입 템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90) 규칙이 완화되어 실수 타입과 [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)을 사용할 수 있습니다.

단,  [리터럴 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type/#%EB%A6%AC%ED%84%B0%EB%9F%B4-%ED%83%80%EC%9E%85)은 모든 멤버 변수가 `public`이고, [mutable](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5-%EC%A7%80%EC%A0%95%EC%9E%90mutable)이 아니고, [constexpr 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/#constexpr-%EC%83%9D%EC%84%B1%EC%9E%90)가 있어야 합니다. 

를 사용할 수 있습니다.

```cpp
template<double d> 
class A_20 {};
A_20<3.14> a; // 실수 타입

class MyClass_11 {
public: 
    int m_Val; // 멤버 변수는 public이고, mutable이 아니어야 합니다.
public: 
    constexpr explicit MyClass_11(int val) : m_Val(val) {} // constexpr 생성자여야 합니다.
};
template<MyClass_11 myClass> 
class B_20 {};
B_20<MyClass_11{1}> b; // 멤버 변수는 public이고, mutable이 없는 constexpr 생성자가 있는 리터럴 타입
```

# (C++20~) 클래스 템플릿 인수 추론시 initializer_list 개선 

C++17 부터 도입된 [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)은 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-initialization/#initializer_list)로 초기화시 [클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0)이 되지 않았습니다.(*[클래스 템플릿 인수 추론](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c17-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0) 참고*)

따라서, `std::vector<int> v_11{1, 2, 3};`와 같이 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 명시해야 했는데요, 

C++20 부터는 [클래스 템플릿 인수 추론시 initializer_list인 경우가 개선](https://tango1202.github.io/mordern-cpp/mordern-cpp-template/#c20-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%88%98-%EC%B6%94%EB%A1%A0%EC%8B%9C-initializer_list-%EA%B0%9C%EC%84%A0)되어 `std::vector v_20{1, 2, 3};`처럼 [템플릿 인자](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-parameter-argument/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%9E%90)를 명시하지 않아도 됩니다.

```cpp
template<typename T>
class A {
public:
    explicit A(T val) {}
};

A<int> a{10}; // ~C++17 이전에는 타입을 명시해야 합니다.
A a_17{10}; // C++17 부터는 인수 타입으로부터 추론합니다.

std::vector<int> v_11{1, 2, 3}; // ~C++20 이전에는 타입을 명시해야 합니다.
std::vector v_20{1, 2, 3}; // initializer_list의 요소 타입으로 부터 vector의 템플릿 인자를 추론합니다.
```