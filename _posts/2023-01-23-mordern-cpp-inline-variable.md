---
layout: single
title: "#23. [Mordern C++] (C++17~) 인라인 변수"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++17~) [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)가 추가되어 헤더 파일에 정의된 변수를 여러개의 cpp에서 [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include) 하더라도 중복 정의 없이 사용할 수 있습니다. 또한, [클래스 정적 멤버 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)를 선언부에서 초기화 할 수 있습니다.

# 개요

기존에는 [인라인 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/)만 있었는데요(*[인라인 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/) 참고*), C++17 부터는 [인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)를 제공합니다.

[인라인 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/)는 

1. 함수 호출을 최적화할 수 있도록 컴파일러에게 요청합니다.
2. 여러 파일에서 [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include) 한 함수가 중복 정의되지 않도록 링커에게 알려줍니다.

의 역할을 가졌는데요,

[인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)는 여러 파일에서 [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include) 한 변수가 중복 정의되지 않도록 링커에게 알려주는 역할만 합니다.

따라서, 헤더 파일에서 [inline](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/) 으로 변수를 선언해 두면, 여러 `cpp` 에서 [#include](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#include) 하여 사용할 수 있습니다.

```cpp
// Test.h 헤더 파일에서,
inline int g_Val_17{10};

// Cpp1 에서
#include "Test.h" // g_Val_17은 inline 변수여서 중복 정의되지 않습니다.
void IncreaseVal1_17() {
    ++g_Val_17; 
}
// Cpp2 에서
#include "Test.h" // g_Val_17은 inline 변수여서 중복 정의되지 않습니다.
void IncreaseVal2_17() { 
    ++g_Val_17;
}

// Cpp3 에서
#include "Test.h" // g_Val_17은 inline 변수여서 중복 정의되지 않습니다.

extern void IncreaseVal1_17(); // 다른 cpp 파일에 있는 함수 선언
extern void IncreaseVal2_17();

IncreaseVal1_17();
IncreaseVal2_17();

EXPECT_TRUE(g_Val_17 == 12); 
```

# 인라인 변수를 이용한 클래스 정적 멤버 변수 정의

[정적 멤버 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%95%EC%A0%81-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98)는 선언과 정의의 분리가 필요하고 정의부에서 초기값을 세팅해야 한다고 언급했는데요(*[멤버 변수, 초기화 리스트](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/) 참고*),

```cpp
class T {
public:
    static int s_m_Val; // 정적 멤버 변수. 별도 정의 필요
};
int T::s_m_Val{10}; // 별도 정의

EXPECT_TRUE(T::s_m_Val == 10);
```

[인라인 변수](https://tango1202.github.io/mordern-cpp/mordern-cpp-inline-variable/)를 이용하면 선언과 정의의 분리가 필요없으며, 클래스 선언부에서 초기화 할 수 있습니다.

```cpp
class T_17 {
public:
    static inline int s_m_Val_17{10}; // 정적 멤버 변수. 인라인 변수여서 클래스 선언부에서 정의 및 초기화가 가능합니다. 
};
EXPECT_TRUE(T_17::s_m_Val_17 == 10);
```