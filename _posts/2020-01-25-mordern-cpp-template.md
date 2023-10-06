---
layout: single
title: "#25. [모던 C++] (C++11~) extern 템플릿, (C++14~) 변수 템플릿, (C++17~) 템플릿 인수 추론"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `extern`으로 템플릿 선언을 할 수 있으며, 템플릿 인스턴스 중복 생성을 없앨 수 있습니다. 
> * (C++14~) 변수 템플릿이 추가되어 변수도 템플릿으로 만들 수 있습니다.
> * (C++17~) 클래스 템플릿 인수 추론이 추가되어 템플릿 함수처럼 타입을 생략할 수 있습니다.
> * (C++17~) 템플릿이 타입이 아닌 개체를 인자로 사용할때 템플릿 인자로 `auto`를 사용할 수 있습니다.

# extern 템플릿

기존에는 템플릿을 헤더 파일에 정의해 두고, 여러 파일에서 인클루드 한다면, 템플릿 정의가 중복 정의되어 코드 크기가 커지는데요([템플릿 인스턴스 중복 생성](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/#%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EC%A4%91%EB%B3%B5-%EC%83%9D%EC%84%B1) 참고),

```cpp
// Test_MordernCpp_ExternTemplate.h 에서

namespace ExternTemplate {
    template<typename T>
    T Add(T a, T b) {
        return a + b;
    }
}

// test1.cpp 에서
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate1) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(1, 2) == 3); // Add<int>()가 정의 되어 포함됩니다.
}

// test2. cpp 에서
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate2) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(10, 20) == 30); // Add<int>가 재정의 되어 포함됩니다.
}
```

C++11 부터는 `extern` 템플릿을 추가하여 템플릿 선언만 할 수 있으며, 불필요한 코드 크기를 최소화 할 수 있습니다. 

```cpp
// Test_MordernCpp_ExternTemplate.h 에서

namespace ExternTemplate {
    template<typename T>
    T Add(T a, T b) {
        return a + b;
    }
}

// test1.cpp 에서
#include "Test_MordernCpp_ExternTemplate.h"

TEST(TestMordern, ExternTemplate1) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(1, 2) == 3); // Add<int>()가 정의 되어 포함됩니다.
}

// test2. cpp 에서
#include "Test_MordernCpp_ExternTemplate.h"

// 이전에 정의된 템플릿을 사용합니다.
extern template int ExternTemplate::Add<int>(int, int);

TEST(TestMordern, ExternTemplate2) {
    using namespace ExternTemplate;

    EXPECT_TRUE(Add(10, 20) == 30); // Add<int>가 재정의 되어 포함됩니다.
}
```

# (C++14~) 변수 템플릿

기존의 템플릿은 클래스와 함수만 지원했는데요([템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/) 참고),

C++14 부터는 변수도 템플릿으로 만들 수 있습니다.

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

다음은 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)에서 소개한 `Factorial` 을 변수 템플릿으로 구현한 예입니다.

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

기존에는 템플릿 함수만 인수를 추론하고 클래스 템플릿은 인수를 추론하지 않았습니다.

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

C++17 부터는 클래스 템플릿도 생성자에 전달된 인수를 추론하므로 다음과 같이 사용할 수 있습니다.

```cpp
A a_17{10}; // C++17 이후부터는 인수로부터 추론합니다.
EXPECT_TRUE(a_17.Func(1, 2) == 3); 
```

# auto 템플릿 인자

C++17 부터는 템플릿이 타입이 아닌 개체를 인자로 사용할때 템플릿 인자로 `auto`를 사용할 수 있습니다.

```cpp
template<int Num>
class A {
public:
    int GetNum() const {return Num;}
};

EXPECT_TRUE(A<10>{}.GetNum() == 10);

template<auto Num>
class A_17 {
public:
    auto GetNum() const {return Num;}
};    
EXPECT_TRUE(A_17<10>{}.GetNum() == 10);
```