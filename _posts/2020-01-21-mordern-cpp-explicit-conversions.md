---
layout: single
title: "#21. [모던 C++] (C++11~) 명시적 형변환"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `explicit` 형변환 연산자를 추가하여 명시적으로 형변환 할 수 있습니다.

# 개요

[형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 에서 암시적 변환이 

[안전하지 않은 bool 형변환 연산자 정의](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%88%EC%A0%84%ED%95%98%EC%A7%80-%EC%95%8A%EC%9D%80-bool-%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98) 에서 암시적으로 형변환 되는 문제점을 언급했는데요, C++11부터는 `explicit` 형변환 연산자를 추가하여 명시적으로만 형변환 할 수 있게 했습니다.

```cpp
class T1 {
public:
    // 암시적 형변환이 됩니다.
    operator bool() {return true;}
};

class T2 {
public:
    // 명시적으로만 형변환 됩니다.
    explicit operator bool() {return true;} 
};

T1 t1;
int val1 = t1; // (△) 비권장. bool() 을 이용하여 형변환 하고 암시적으로 int로 변환합니다.

T2 t2;
int val2 = t2; // (X) 컴파일 오류
bool val3 = static_cast<bool>(t2); // 명시적으로 변환해야 사용할 수 있습니다.
```