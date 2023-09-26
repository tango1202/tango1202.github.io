---
layout: single
title: "#8. [고전 C++ STL] pair"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **모던 C++**
> * `tuple`은 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.([tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/) 참고)

# 개요

`pair`는 두개의 데이터를 저장합니다. 함수의 리턴값등에서 두개의 값을 리턴해야 할 때 일일이 클래스를 만들어 이용하기 보다는 `pair`를 이용하여 가볍게 전달할 수 있습니다.

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




