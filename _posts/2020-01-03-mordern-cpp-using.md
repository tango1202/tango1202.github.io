---
layout: single
title: "#3. [모던 C++] (C++11~) using을 이용한 타입 별칭"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
> * [MEC++#9] typedef 보다 별칭 선언을 선호하라.([템플릿 클래스 별칭 지원](??))

> * (C++11~) [using을 이용한 타입 별칭이 추가](??)되어 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 보다 좀 더 직관적인 표현이 가능해 졌습니다.

# 개요

기존에는 타입 별칭을 위해 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)를 사용했었는데요([타입 별칭](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 참고),

C++11 부터는 [using을 이용한 타입 별칭이 추가](??)되었습니다.

[using을 이용한 타입 별칭](??)은 다음과 같이 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)와 별칭의 순서가 다릅니다만, 좀 더 직관적입니다.

```cpp
typedef unsigned long ulong;
using ulong_11 = unsigned long; // typedef와 순서가 반대입니다.
```

[함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)인 경우는 훨씬 더 직관적입니다.

```cpp
typedef void (*Func)(int); 
using Func_11 = void (*)(int);
```

# 템플릿 클래스 별칭

[using을 이용한 타입 별칭](??)은 템플릿 클래스 별칭도 지원합니다.

```cpp
// typedef는 템플릿의 구체화된 타입만 지원합니다.
typedef std::vector<int> MyVector;

 // using은 템플릿을 지원합니다.
template<typename T>
using MyVector_11 = std::vector<T>; 

MyVector_11<int> v;
```

