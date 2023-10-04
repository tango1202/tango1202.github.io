---
layout: single
title: "#31. [모던 C++] (C++14~) 변수 템플릿(작성중)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++14~) 변수도 템플릿으로 만들 수 있습니다.

# 개요

기존의 템플릿은 클래스와 함수만 지원했는데요([템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/) 참고),

C++14 부터는 변수도 템플릿으로 만들 수 있습니다.

다음 예는 동일한 값을 지정한 타입에 따라 다른 정밀도로 사용하는 예입니다.

```cpp
template<class T>
// constexpr T pi{3.1415926535897932385L}; // 중괄호 초기화는 암시적 형변환이 안되서 = 로 초기화 합니다.
constexpr T pi = 3.1415926535897932385L; 

// 동일한 값을 타입에 따라 서로 다른 정밀도로 사용할 수 있습니다.
std::cout << "pi<double> : " << std::setprecision(10) << pi<double> << std::endl;
std::cout << "pi<float> : " << std::setprecision(10) <<pi<float> << std::endl;
std::cout << "pi<int> : " << std::setprecision(10) <<pi<int> << std::endl;
```

```cpp
pi<double> : 3.141592654
pi<float> : 3.141592741 // double 보다 정밀도가 낮습니다.
pi<int> : 3
```

또한, 템플릿 특수화를 이용하여 템플릿 메타 프로그래밍에 활용할 수 있습니다. 

다음은 [템플릿 메타 프로그래밍](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-meta-programming/)에서 소개한 `Factorial` 을 변수 템플릿으로 구현한 예입니다.

```cpp
// 1을 뺀 값을 재귀 호출합니다.
template<int val> 
constexpr int factorial = val * factorial<val - 1>; 

// 1일때 특수화 버전. 더이상 재귀 호출을 안합니다.
template<>
constexpr int factorial<1> = 1;

EXPECT_TRUE(factorial<5> == 5 * 4 * 3 * 2 * 1);
```
