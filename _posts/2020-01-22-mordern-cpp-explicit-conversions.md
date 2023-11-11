---
layout: single
title: "#22. [모던 C++] (C++11~) explicit 형변환 연산자, (C++20~) explicit(bool)"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [explicit 형변환 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/)가 추가되어 [명시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 할 수 있습니다.

# 개요

기존에는 [암시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98) 되는 문제점이 있으니 형변환 연산자를 정의하지 말라고 했는데요([형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)와 [안전하지 않은 bool 형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%88%EC%A0%84%ED%95%98%EC%A7%80-%EC%95%8A%EC%9D%80-bool-%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 참고), 

C++11 부터는 [explicit 형변환 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-explicit-conversions/)를 추가하여 명시적으로만 형변환 할 수 있게 했습니다.

```cpp
class T {
public:
    // 암시적 형변환이 됩니다.
    operator bool() {return true;}
};

class T_11 {
public:
    // 명시적으로만 형변환 됩니다.
    explicit operator bool() {return true;} 
};

T t;
int val1{t}; // (△) 비권장. bool() 을 이용하여 형변환 하고 암시적으로 int로 변환합니다.

T_11 t_11;
// int val2{t_11}; // (X) 컴파일 오류
bool val3{static_cast<bool>(t_11)}; // 명시적으로 변환해야 사용할 수 있습니다.
```

# (C++20~) explicit(bool)

기존에는 형변환을 막기 위해서 [explicit](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)를 사용했습니다.

```cpp
template<typename T>
class A {
    T m_Val;
public:
    explicit A(T val) : m_Val{val} {} // 모든 타입에 대해 형변환을 차단합니다.
};

A<int> a{0};
A<int> b = 0; // (X) 컴파일 오류. explicit로 차단했습니다.

A<std::string> c{"Hello"};
A<std::string> d = std::string("World"); // (X) 컴파일 오류. explicit로 차단했습니다.
```

하지만 모든 타입에 대해서 형변환을 차단하는데요,

C++20 부터는 [explicit(bool)](??)이 추가되어 특정 조건일 때만 [explicit](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)로 동작하게 할 수 있습니다.

다음 예는 `int` 타입인 경우에만 [explicit](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1-%EC%A7%80%EC%A0%95%EC%9E%90explicit)로 동작하고, 그외 타입은 [암시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)됩니다.

```cpp
template<typename T>
class A_20 {
    T m_Val;
public:
    // 정수 타입인 경우만 explicit 입니다.
    explicit(std::is_integral<T>::value) A_20(T val) : m_Val{val} {}
};

A_20<int> a{0};
A_20<int> b = 0; // (X) 컴파일 오류. explicit로 차단했습니다.

A_20<std::string> c{"Hello"};
A_20<std::string> d = std::string("World"); // (O) 정수 타입이 아니어서 암시적 형변환을 허용합니다.       

```
