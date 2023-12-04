---
layout: single
title: "#14. [고전 C++ STL] bitset"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

[bitset](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-bitset/)은 비트 단위의 데이터를 관리하는 개체입니다.

다음과 같이 16진수나 [string](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)으로 초기화 할 수 있고, `set()`, `reset()`함수로 특정 비트를 설정하며, `test()`함수로 값을 조회합니다.

```cpp
std::bitset<10> b1; // 10개의 비트 플래그로 구성됨. all 0 
std::bitset<16> b2 = 0xaaaa; // 16진수로 초기화 할 수 있습니다. 0x1010 1010 1010 1010 
std::bitset<10> b3(std::string("1010101010")); // 문자열로 초기화 할 수 있습니다. 0x1010 1010 10 
std::bitset<100> b4; // 100개의 비트 플래그로 구성됩니다. all 0

b1.set(1); // 1번 비트 true 
b1.reset(2); // 2번 비트 false
b4.set(99); // 99번 비트 true

EXPECT_TRUE(b1.test(1) == true);
EXPECT_TRUE(b1.test(2) == false);
EXPECT_TRUE(b4.test(99) == true);
EXPECT_TRUE(b4.test(100) == false); // (X) 예외 발생. 범위를 벗어나면 예외를 발생합니다.
```


