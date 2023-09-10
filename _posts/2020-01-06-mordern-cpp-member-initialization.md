---
layout: single
title: "#6. [모던 C++] (C++11~) 멤버 선언부 초기화"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 멤버 선언부에서 초기화를 할 수 있어 초기화 작성이 쉬워졌습니다.

# 개요

기존에는 [초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)에서만 멤버 변수를 초기화 할 수 있었는데요, C++11부터는 멤버 변수 선언시에도 초기화 할 수 있습니다.
단, 정적 멤버 변수는 기존과 동일하게 별도 정의부에서 초기화해야 합니다.

```cpp
class A {
public:
    explicit A(int, int) {}    
};

class T {
    int m_A = 0; // C++11. 멤버 변수 초기화
    int m_B{0}; // C++11. 중괄호 초기화
    int m_C = {0}; // C++11. 중괄호 초기화
    int* m_D = nullptr; // C++11. 포인터 초기화
    std::vector<int> m_E{1,2,3}; // C++11. 중괄호 초기화
    A m_F{10, 20}; // C++11. 중괄호 초기화
    // A m_F(10, 20); // (X) 컴파일 오류. A를 리턴하는 m_F 함수 정의

    const int m_G = 0; // 기존과 동일하게 상수 멤버 변수 초기화
    static const int m_H = 0; // 기존과 동일하게 선언부에 정적 상수 멤버 변수 초기화
    // static int m_H = 0; // (X) 컴파일 오류. 기존과 동일하게 정적 멤버 변수는 별도 초기화해야 함 
};  
```

