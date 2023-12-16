---
layout: single
title: "#31. [Mordern C++ STL] (C++14~) exchange()"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++14~) [exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)가 추가되어 주어진 값을 바꾸는 작업이 간편해 졌습니다. 특히 [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.

# 개요

[exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)는 주어진 값을 바꾸고 이전값을 리턴합니다.

기존에 [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85--%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)는 다음과 같이 구현됩니다.

```cpp
class Big {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

    Big(Big&& other) noexcept : 
        m_Size(other.m_Size),
        m_Ptr(other.m_Ptr) {

        other.m_Size = 0; // other는 0, nullptr로 초기화 됩니다.
        other.m_Ptr = nullptr;
    } 
    // 이동 대입 연산자. 우측값(rvalue) 이동
    Big& operator =(Big&& other) noexcept {
        delete[] m_Ptr; // 기존 것은 삭제하고,
        m_Size = other.m_Size; // other 것을 저장한뒤
        m_Ptr = other.m_Ptr;

        other.m_Size = 0; // other는 초기화 합니다.
        other.m_Ptr = nullptr;
        return *this;
    }   
};
```

[exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)를 사용하면 다음과 같이 좀 더 간소하게 작성할 수 있습니다.

```cpp
class Big_14 {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

    Big_14(Big_14&& other) noexcept : 
        m_Size{std::exchange(other.m_Size, 0)}, // other는 0, nullptr로 초기화 됩니다.
        m_Ptr{std::exchange(other.m_Ptr, nullptr)} {} 
    // 이동 대입 연산자. 우측값(rvalue) 이동
    Big_14& operator =(Big_14&& other) noexcept {
        delete[] m_Ptr; // 기존 것은 삭제하고,

        m_Size = std::exchange(other.m_Size, 0);  // other는 0, nullptr로 초기화 됩니다.
        m_Ptr = std::exchange(other.m_Ptr, nullptr);

        return *this;
    }   
};
```

