---
layout: single
title: "#21. [모던 C++ STL] hash(C++11)" 
categories: "cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [hash()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-hash/)가 추가되어 각 타입별로 데이터의 [해시값(*Digest*)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)을 구할 수 있습니다.

# 개요

[hash()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-hash/)가 추가되어 각 타입별로 데이터의 [해시값(*Digest*)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)을 구할 수 있습니다.

1. 리턴되는 [해시값(Digest)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)은 `size_t` 입니다.(4byte보다 크거나 같으며, `int`와 동일한 크기입니다.)
2. 동일한 데이터는 동일한 [해시값(Digest)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)이 나와야 합니다.
3. 서로 다른 데이터라도 [해시값(Digest)](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C)은 충돌될 수 있습니다. 확률적으로는 `1.0 / std::numeric_limits<std::size_t>::max()` 입니다.

STL 에서는 [bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/), `int`등의 기본 타입과 [string](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-string/)등의 STL 타입에 대해 모두 정의되어 있어 이를 사용할 수 있습니다.


```cpp
int a{1};
int b{1};
int c{2};

size_t hashA{std::hash<int>{}(a)};
size_t hashB{std::hash<int>{}(b)};
size_t hashC{std::hash<int>{}(c)};

EXPECT_TRUE(hashA == hashB); // 같은 값은 해시값도 같습니다.
EXPECT_TRUE(hashA != hashC); // 다른값은 해시값도 다릅니다.

std::string str1{"Hello"};
std::string str2{"Hello"};
std::string str3{"Hi"};

size_t hashStr1{std::hash<std::string>{}(str1)};
size_t hashStr2{std::hash<std::string>{}(str2)};
size_t hashStr3{std::hash<std::string>{}(str3)};

EXPECT_TRUE(hashStr1 == hashStr2); // 같은 값은 해시값도 같습니다.
EXPECT_TRUE(hashStr1 != hashStr3); // 다른값은 해시값도 다릅니다.
```

[hash()](https://tango1202.github.io/cpp-stl/modern-cpp-stl-hash/)는 [해시 컨테이너](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set/#%ED%95%B4%EC%8B%9C-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)에서 사용될 수 있고요, 자세한 사용 방법은 [unordered_map, unordered_multimap, unordered_set, unordered_multiset](https://tango1202.github.io/cpp-stl/modern-cpp-stl-unordered_map-unordered_set)을 참고하시기 바랍니다.