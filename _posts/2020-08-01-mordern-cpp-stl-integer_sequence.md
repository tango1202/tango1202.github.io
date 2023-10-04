---
layout: single
title: "#29. [모던 C++ STL] (C++14~) integer_sequence"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

`integer_sequence` 는 컴파일 타임에 정수 타입의 시퀀스를 만듭니다. 템플릿 함수의 인자로 사용하여 파라메터 팩 배포 및 확장에 사용할 수 있습니다.

`array`

```cpp
std::array<int, 5> arr{10, 20, 30, 40, 50};
std::tuple<int, int> data{std::make_tuple(arr[0], arr[1])};
EXPECT_TRUE(
    std::get<0>(data) == 10 &&
    std::get<1>(data) == 20 
); 
```


```cpp
template <size_t N, std::size_t... params>
auto Func(const std::array<int, N>& arr, std::integer_sequence<size_t, params...>) {
    return std::make_tuple(arr[params]...); // arr[param1], arr[param2], arr[param3] 등으로 전개됨 
}
```