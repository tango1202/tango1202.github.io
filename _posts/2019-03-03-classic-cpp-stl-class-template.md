---
layout: single
title: "#3. [고전 C++ STL] 클래스 템플릿"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---



# template 인자 - 타입

```cpp
template <typename T, typename G>
class String {};

String<char, int> s;
```

# template 인자 - 인수

```cpp
template <typename T, T val>
class String {};
String<char, 'a'> s;
```

# 비 template 인자

```cpp
template <typename T, int val>
class String {};

String<char, 100> s;

String<char, 100> s1; String<char, 101> s2; s2 = s1; // (X) 타입이 다르다.
```


