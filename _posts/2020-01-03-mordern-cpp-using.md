---
layout: single
title: "#3. [모던 C++] (C++11~) using을 이용한 타입 별칭"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---
> * [MEC++#9] typedef 보다 별칭 선언을 선호하라.([클래스 템플릿 별칭 지원](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/#%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B3%84%EC%B9%AD))

> * (C++11~) [using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/)이 추가되어 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 보다 좀 더 직관적인 표현이 가능해 졌습니다.

# 개요

기존에는 타입 별칭을 위해 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)를 사용했었는데요(*[타입 별칭](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 참고*),

C++11 부터는 [using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/)이 추가되었습니다.

[using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/)은 다음과 같이 [typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)와 별칭의 순서가 다릅니다만, 좀 더 직관적입니다.

```cpp
typedef unsigned long ulong;
using ulong_11 = unsigned long; // typedef와 순서가 반대입니다.
```

[함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0)인 경우는 훨씬 더 직관적입니다.

```cpp
typedef void (*Func)(int); 
using Func_11 = void (*)(int);
```

# 클래스 템플릿 별칭

[using을 이용한 타입 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/)은 [클래스 템플릿 별칭](https://tango1202.github.io/mordern-cpp/mordern-cpp-using/#%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%85%9C%ED%94%8C%EB%A6%BF-%EB%B3%84%EC%B9%AD)도 지원합니다.

[typedef](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD)를 사용하여 별칭을 작성하면, 템플릿의 경우 구체화된 타입으로만 별칭이 작성되어 향후 다른 타입으로 변경할 수 없지만,

```cpp
// typedef는 템플릿의 구체화된 타입만 지원합니다.
typedef std::vector<int> MyVector;

MyVector v; // std::vector<int> 타입입니다. int 외 다른 타입으로 정의할 수 없습니다.
```

`using`을 사용하면 템플릿 별칭을 이용해서도 여러가지 타입으로 템플릿 인스턴스화를 할 수 있습니다.

```cpp
// using은 템플릿을 지원합니다.
template<typename T>
using MyVector_11 = std::vector<T>; 

MyVector_11<int> v1; // std::vector<int> 타입입니다.
MyVector_11<char> v2 : // std::vector<char> 타입입니다.
```

