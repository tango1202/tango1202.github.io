---
layout: single
title: "#11. [모던 C++] (C++11~) using을 이용한 타입 별칭, 템플릿 클래스 별칭"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `using`을 이용한 타입 별칭이 추가되어 `typedef` 보다 좀 더 직관적인 표현이 가능해 졌습니다. 

# using 타입 별칭

기존에는 타입 별칭을 위해 `typedef`를 사용했었습니다만, `using`이 추가되었습니다.

`using`은 다음과 같이 `typedef`와 별칭의 순서가 다릅니다만, 좀 더 직관적입니다.

```cpp
typedef unsigned long ulong;
using ulong = unsigned long; // typedef와 순서가 반대입니다.
```

# 템플릿 클래스 별칭

`using`은 템플릿 클래스 별칭을 지원합니다.

```cpp
// typedef는 템플릿의 구체화된 타입만 지원합니다.
typedef std::vector<int> MyVector;

 // using은 템플릿을 지원합니다.
template<typename T>
using MyVector = std::vector<T>; 

MyVector<int> v;
```