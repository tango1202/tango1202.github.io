---
layout: single
title: "#8. [Legacy C++ STL] pair"
categories: "legacy-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **Mordern C++**
> * (C++11~) [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/)이 추가되어 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 좀 더 간편하게 만들 수 있습니다.
> * (C++17~) [구조화된 바인딩](https://tango1202.github.io/mordern-cpp/mordern-cpp-structured-binding/)이 추가되어 [배열](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-array/), [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/), [tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/), 클래스등의 내부 요소나 [멤버 변수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-variable/)에 쉽게 접근할 수 있습니다.

# 개요

[pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)는 두개의 데이터를 저장합니다. 함수의 [리턴값](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)등에서 두개의 값을 리턴해야 할 때 일일이 클래스를 만들어 이용하기 보다는 [pair](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-pair/)를 이용하여 가볍게 전달할 수 있습니다.

```cpp
std::pair<int, char> f() {
    return std::pair<int, char>(10, 'a');
}
std::pair<int, char> result = f();
EXPECT_TRUE(result.first == 10 && result.second == 'a');
```

`make_pair()`를 이용하면 좀더 단순하게 만들 수 있습니다.

```cpp
std::pair<int, char> g() {
    return std::make_pair(10, 'a');
}
std::pair<int, char> result = g();
EXPECT_TRUE(result.first == 10 && result.second == 'a');        
```




