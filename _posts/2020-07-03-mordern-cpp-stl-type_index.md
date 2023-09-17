---
layout: single
title: "#3. [모던 C++ STL] (C++11~) type_index"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `type_index`가 추가되어 `type_info`를 컨테이너에서 관리할 수 있습니다.

# 개요

`typeid()` 는 `const type_info&`를 리턴하는데요([typeid 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#typeid-%EC%97%B0%EC%82%B0%EC%9E%90) 참고), 이는 [컨테이너 요소 규칙](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%9A%94%EC%86%8C-%EA%B7%9C%EC%B9%99)인 복사 생성자나 연관 컨테이너를 위한 대소 비교를 제공하지 않아 컨테이너에 추가할 수 없습니다.

C++11 부터는 `const type_info&`를 컨테이너의 요소로 사용할 수 있게끔 복사 생성자와 대소 비교를 구현한 래퍼인 `type_index`를 제공합니다. 

이에 따라 타입에 따른 부가 정보를 관리하는 컨테이너를 좀 더 쉽게 만들 수 있습니다.

```cpp
class A {};
class B {};

// const std::type_info& 여서 컨테이너에서 사용할 수 없습니다.
// const std::type_info& a{typeid(A)};
// const std::type_info& b{typeid(B)};

std::type_index a{typeid(A)};
std::type_index b{typeid(B)};

std::map<std::type_index, int> m;

m.insert(std::make_pair(a, 0));
m.insert(std::make_pair(b, 1));

EXPECT_TRUE(m[typeid(A)] == 0 && m[typeid(B)] == 1);
```